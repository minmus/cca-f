# The persistent case facts block: who builds it, and how

**Stage 1** of the [learning path](../study-map/learning-path.md) · **D5 › 5.1 Context Window Management**
**Builds on:** [S01-2-context-window-across-sessions](S01-2-context-window-across-sessions.md) (the model forgets everything after each request) · [S01-4-external-memory](S01-4-external-memory.md) (if it must be remembered, code saves it)
**Goes with:** [Q46](../questions/Q46-stateless-api-pass-full-history/question.md) (the API is stateless) · [Q40](../questions/Q40-multi-issue-case-facts-layer/question.md) (Stage 8)

## The question

> How is a persistent case facts block created or managed? Is it something Claude has by default, or must the user create it and point Claude to it?

## Short answer

**It's not built in.** It's a pattern that **your application code** builds. You don't point Claude to it either. Your code **pastes it into every request**, so Claude simply sees it at the top each time.

Same rule as in S01-2: **the model forgets everything after each request, so "memory" is always a program re-sending saved text.** The case facts block is the small, exact part of that text.

Guide 5.1: *"Extract transactional facts (amounts, dates, order numbers, statuses) into a structured block included in every prompt, outside the summarised history. The block is never summarised, so it persists across every turn whatever happens to the history."*

## Why it's needed

Summaries keep the gist and drop the details. Guide 5.1: *"By default, a summary of transactional data keeps the general meaning and drops the specific details."* Its example:

```
Turn 3:   "I'd like a refund of $247.83 for order #8891 placed on March 3rd"
Summary:  "Customer wants a refund for a recent order"
```

The amount, the order number and the date are gone, and the agent needs all three to process the refund.

## How your code manages it: four steps

| Step | What happens | Guide 5.1 |
|---|---|---|
| **1. Extract** | After each tool result, code copies the key facts out of it | *"A function that takes raw tool output and returns a structured object containing only the transactional facts: customer ID, order numbers, amounts, dates, and statuses."* |
| **2. Store** | Your app keeps that object for the conversation (a variable, a database row) | *(practical detail, not guide wording)* |
| **3. Inject** | Every request is built with the block at the top | *"A prompt construction function that always includes the case facts block at the top of every message, followed by any summarised history, followed by the current turn. The case facts block should be clearly delimited with a section header."* |
| **4. Update, never summarise** | A status change updates its entry. A new problem gets its own entry. When old turns are summarised, the block is left out of the summary | *"Each issue gets its own entry with order IDs, amounts and statuses."* |

## What one request looks like

```
system:
  You are the support agent for …
  ## CASE FACTS (exact, never summarised)
  { "customerId": "C-4421",
    "issues": [ { "orderId": "#8891", "orderDate": "2024-03-03",
                  "refundAmount": "$247.83", "status": "pending_refund" } ] }
  ## Earlier conversation (summary)
  Customer wants a refund for a recent order.     ← vague, and that's fine
messages:
  … recent turns …, current turn
```

## In code (illustration only)

```python
case_facts = {"customerId": None, "issues": {}}          # kept by your app, one per conversation

def after_tool_call(name, result):                       # 1. extract and 4. update: code, not the model
    if name == "lookup_order":
        case_facts["issues"][result["order_id"]] = {
            "amount": result["total"], "date": result["order_date"], "status": result["status"]}
    elif name == "issue_refund":                         # same issue, so update its entry
        case_facts["issues"][result["order_id"]]["status"] = result["status"]

def build_system(summary):                               # 3. inject on every request
    return (INSTRUCTIONS
            + "\n\n## CASE FACTS (exact, never summarised)\n" + json.dumps(case_facts)
            + "\n\n## Earlier conversation (summary)\n" + summary)
```

*Practical tip, not guide wording:* if a customer types an amount that no tool has returned yet, confirm it with a tool lookup and store the tool's value. Same rule as S01-4 Idea 3: code takes the value straight from the tool result, so nothing gets restated wrongly.

## How it differs from other kinds of "memory"

| | Case facts block | External memory ([S01-4](S01-4-external-memory.md)) | CLAUDE.md / auto memory |
|---|---|---|---|
| Where it lives | **Inside every request** | Outside the context, fetched through a tool when needed | Files Claude Code loads at session start |
| Size | Small: only the key facts | Can be huge | Instructions and notes |
| Built in? | **No, your code builds it** | No, your code plus tools | **Yes, in Claude Code** |

Newer API features don't replace it. Guide 5.1 notes that the docs now add context editing, then says: *"Dropping messages without a plan loses facts; use case facts blocks and summarisation."*

> **The case facts block is a few exact facts that your code copies from tool results and pastes into every request, outside the summary. Claude never has to "remember" them: they're always in front of it.**

## Check questions

- *Check: a colleague says, "Skip the block. Just put this in the system prompt: Always remember every amount and order number the customer mentions." Turns 1–4 have been summarised into "Customer wants a refund for a recent order." Why can't Claude follow that instruction at turn 5?*
  *My answer:* "Because the conversation itself gets summarised after a while." 🟡 Summarisation causes the loss, but the *why the instruction can't help* was missing:
  - **Claude only has what's in this request.** At turn 5 the instruction arrives, but `$247.83` doesn't: it isn't in the summary. "Remember" has nothing to work on, because Claude keeps nothing between requests. It's like saying "don't forget the PIN" after the note with the PIN was thrown away.
  - **Even reaching the summariser, it's only a request.** A prompt saying "keep the numbers" works most of the time, not every time, and a number can be restated slightly wrong. Guide 5.1 lists *"telling the model to attend to everything"* as a trap for this problem.

- *Check: with the block in place, at turn 5, where is `$247.83` in the request, and which part of your code put it there?*
  *My answer:* "`build_system`" 🟡 Right for putting it into the request, but there are two pieces of code and one place:
  - **Where:** in the system prompt, under `## CASE FACTS`, above the summary.
  - **`after_tool_call`** copied it from the `lookup_order` result into `case_facts`, **once**.
  - **`build_system`** pastes `case_facts` into the system prompt **on every request**.
  - You need both: without the first there's nothing to paste, and without the second Claude never sees it.

- *Check: at turn 6 the refund goes through, and `issue_refund` returns `{"order_id": "#8891", "status": "refunded"}`. What has to change in the block, and which function makes that change?*
  *My answer:* "`after_tool_call` must put the fact that the refund was successful into the case facts." ✅ Correct: it's the code that watches tool results. To be precise, it **updates the existing #8891 entry** (`pending_refund` → `refunded`) rather than adding a new fact, because it's the same issue. `build_system` doesn't change: from turn 7 onward it pastes the new status automatically.

- *Check: suppose `after_tool_call` had no `issue_refund` branch, so the block still says `pending_refund` after the refund went through. What could go wrong at turn 7, when the customer asks "so is my refund done?"*
  *(Your answer and the feedback will be recorded here.)*

## Sources

- Guide D5 › 5.1 Context Window Management (checked 2026-11-05):
  - *"The fix is a persistent case facts block. Extract transactional facts (amounts, dates, order numbers, statuses) into a structured block included in every prompt, outside the summarised history. The block is never summarised, so it persists across every turn whatever happens to the history."*
  - *"By default, a summary of transactional data keeps the general meaning and drops the specific details."*
  - *"Each issue gets its own entry with order IDs, amounts and statuses."*
  - *"The persistent case facts block is the single most important pattern in context window management."*
  - *"Traps include telling the model to attend to everything and keeping full tool results as a precaution."*
  - *"Dropping messages without a plan loses facts; use case facts blocks and summarisation."*
  - Build exercise: *"A function that takes raw tool output and returns a structured object containing only the transactional facts"*; *"A prompt construction function that always includes the case facts block at the top of every message, followed by any summarised history, followed by the current turn."*
