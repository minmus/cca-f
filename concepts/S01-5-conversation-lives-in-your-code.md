# The conversation lives in your code (Udemy B #12 lab)

**Stage 1** of the [learning path](../study-map/learning-path.md) (Udemy B #12 Practice: The Conversation Lives in Your Code) · **D5 › 5.1 Context Window Management** · **D1 › 1.1 Agentic Loops**
**Builds on:** [S01-2-context-window-across-sessions](S01-2-context-window-across-sessions.md) (the API is stateless) · [Q46](../questions/Q46-stateless-api-pass-full-history/question.md)

## The lab setup code

```python
# --- Lab setup (provided - just run it) ---
import shopassist_lab
from shopassist_lab import check

# Everything below this line is ordinary Claude API code.
from dotenv import load_dotenv
from anthropic import Anthropic

load_dotenv()
client = Anthropic()

MODEL = "claude-sonnet-5-5"

SYSTEM = (
    "You are ShopAssist, an online store's support assistant. "
    "Returns are accepted within 30 days of delivery. "
    "Answer in one or two sentences."
)

TOKEN_LOG = []
```

## What it does, step by step

This is only **setup**. Nothing is sent to Claude yet.

1. **Lab helpers** (`shopassist_lab`, `check`): from the course. `check` probably tests your answers to the lab tasks; its code isn't visible, so that's a guess. **Not part of the Claude API.**
2. **`load_dotenv()`**: reads a `.env` file, for example `ANTHROPIC_API_KEY=sk-ant-...`, into environment variables. The key stays **out of the code** (the same idea as Q131: secrets in env vars).
3. **`client = Anthropic()`**: the object you'll call later with `client.messages.create(...)`. It finds the API key in the environment automatically. **It doesn't open a session or store any history.**
4. **`MODEL`**: one constant, so switching models means changing one line.
5. **`SYSTEM`**: who Claude is (a role), one business rule (30 days) and the answer length. It's sent with **every** request, because the API remembers nothing (5.1).
6. **`TOKEN_LOG = []`**: later each request's **input**-token count is added to it, so you can watch the input grow as the history gets longer.

> **So far: key, client, model, rules. There's no conversation yet. Your code will have to keep that itself, in a list.**

- *Check: why will the numbers in `TOKEN_LOG` probably grow with each new question?*
  *My answer:* "Because it will count input and output tokens and messages will add up." 🟡 "Messages add up" is the key. One correction: `TOKEN_LOG` notes only **input** tokens. The input grows because your code **re-sends the whole history on every request**, and Claude's **earlier replies become part of the next input**:

  ```text
  Request 1 input: SYSTEM + Q1                          → ~60 tokens
  Request 2 input: SYSTEM + Q1 + A1 + Q2                → ~110
  Request 3 input: SYSTEM + Q1 + A1 + Q2 + A2 + Q3      → ~160
  ```

  *(The token counts are made-up examples.)*

  > **Every past question and answer is paid for again on every later request.** That's the cost of a stateless API (5.1), and it's why long chats get compacted.

## Part 2 of the lab: `ask()` (very slowly, one line at a time)

```python
def ask(messages):
    response = client.messages.create(
        model=MODEL,
        max_tokens=200,
        output_config={"effort": "low"},
        system=...,     # the SYSTEM constant         → SYSTEM
        messages=...,   # the messages list received  → messages
    )
    reply = None
    for block in response.content:
        if block.type == ...:   # → "text"
            reply = block.text
    print(reply)
    print("input tokens:", response.usage.input_tokens)
    TOKEN_LOG.append(response.usage.input_tokens)
    return reply

first_reply = ask([{"role": "user", "content": "I bought a jacket 20 days ago."}])
second_reply = ask([{"role": "user", "content": "Can I still return it?"}])
check("stateless", first_reply=first_reply, second_reply=second_reply)
```

### Line 1: `response = client.messages.create(...)`

**It sends one request to Claude and waits for the answer.**

1. `client` is the connection object created in setup, and it holds the API key.
2. `.messages.create(...)` **sends** everything inside the brackets (model, rules, messages) over the internet to Anthropic's servers: the `POST /v1/messages` endpoint.
3. Claude reads it and writes an answer.
4. The answer comes back, and Python stores it in the variable **`response`**.

> **One call = one request = one answer.** Nothing else happens, and nothing is remembered afterwards.

- *Check: if you called `client.messages.create(...)` twice, how many separate requests would go to Claude?*
  *My answer:* "Two separate messages." ✅ Two separate requests. They're **independent**: the second knows nothing about the first unless **your code** puts it into the second one.

### `messages=...`: are older conversation turns supposed to go there?

**Yes. `messages=` is where the conversation so far goes.** It's the **only** place Claude can learn what was said earlier. It's a list of turns that alternate between `user` and `assistant`, and the **new question comes last**:

```python
messages=[
    {"role": "user",      "content": "I bought a jacket 20 days ago."},   # earlier turn
    {"role": "assistant", "content": "Thanks! How can I help?"},          # Claude's earlier reply
    {"role": "user",      "content": "Can I still return it?"},           # NEW question
]
```

- It holds earlier turns of **this** conversation, not unrelated old chats. You add only what's relevant.
- **Claude's own replies go in too**, with `role: "assistant"`. Your code has to save them and put them back.

In the lab's experiment, each call gets a list with **one message only**, so the earlier turn is missing **on purpose**. That's what `check("stateless")` is about.

> **`messages` = the conversation history your code sends. If you leave something out, Claude never saw it.**

- *Check: in the lab's second call, `messages` holds only "Can I still return it?". What does Claude not know when it answers?*
  *My answer:* "The first call and its output are not known." ✅ Right. Concretely, Claude doesn't know **what "it" is** (the jacket was only in call 1) or **when it was bought** (the 20 days), so it can't apply the 30-day rule from `SYSTEM`. It will probably answer something like *"Returns are accepted within 30 days of delivery. When did you receive the item?"*: it has the rule but not the facts. The fix (presumably the lab's next step): put the first message **and** Claude's first reply into the second call's `messages`.

### Running it (2026-11-06)

**Can it run in this repo?** Not as is. A check here found Python 3.12.10, but no `anthropic`, no `python-dotenv`, no `shopassist_lab` and no API key. The work email gets *"You aren't a member of any organization under your email domain"* in the Claude Console, so no key that way. **But the course's own environment doesn't need one:** the traceback showed `MockMessages.create` in `~/code/shopassist_lab.py`. The lab uses a **mock client** that simulates Claude: no key, no cost.

**First run:** `BadRequestError: You still have ... in this request (messages, system).` The blanks were unfilled. Fix: `system=SYSTEM`, `messages=messages`, `block.type == "text"`.

**Second run:**

```text
Thanks for letting me know. Since you bought the jacket 20 days ago, you still have about 10 days left in our 30-day return window.
input tokens: 7

Which item would you like to return, and when was it delivered? Then I can check it against our 30-day return window.
input tokens: 5
[PASS] Task stateless - Two questions sent on their own
  The second answer asks which item you mean. Claude did not forget the jacket - it was never told. Every request stands alone, and Claude sees only what that request contains.
```

| Call | Claude saw | Answer |
|---|---|---|
| 1 | "I bought a jacket 20 days ago." | Uses the facts and applies the 30-day rule: *"about 10 days left"* |
| 2 | "Can I still return it?" **only** | Has the rule, **not the facts**: *"Which item… when was it delivered?"* |

> *"Claude did not forget the jacket - it was never told."* (the lab's checker)

*Note:* `input tokens: 7` / `5` are the **mock's** simplified counts (roughly the message's words). A real call would also count the `SYSTEM` text, so the numbers would be bigger.

- *Check: to make the second call answer "Yes, you have about 10 days left," what exactly must its `messages` list contain? Name the items in order.*
  *My answer:* "It must contain history." 🟡 Right idea, but abstract: the question asked for the **items in order**. Concretely:

  ```python
  messages=[
      {"role": "user",      "content": "I bought a jacket 20 days ago."},   # 1. first question
      {"role": "assistant", "content": first_reply},                        # 2. Claude's first answer
      {"role": "user",      "content": "Can I still return it?"},           # 3. the new question, LAST
  ]
  ```

  Three details an exam option could get wrong: **Claude's own reply is included** (`role: "assistant"`); **the order alternates** user, assistant, user; **the new question goes last**.

  > **"History" means: every earlier user turn and every earlier assistant reply, in order, then the new question.**

## Task 2: keep the conversation in a list

```python
history = []
TOKEN_LOG.clear()   # provided: count only this conversation's turns

def say(text):
    history.append({"role": "user", "content": text})          # the customer
    reply = ask(history)                                       # the whole conversation
    history.append({"role": "assistant", "content": reply})    # ShopAssist

say("I bought a jacket 20 days ago.")
print()
say("Can I still return it?")
check("history", history=history)
```

`history` is the conversation's memory: a list kept in **your** code. `say()` does the bookkeeping:
1. Add the customer's message (`user`).
2. Send the **whole** list.
3. Add Claude's reply (`assistant`).

```text
Turn 1 sends: [user: jacket]
Turn 2 sends: [user: jacket, assistant: "10 days left…", user: return it?]   ← all 3
```

> **`history` is the memory. `say()` does the bookkeeping: add the question, send everything, add the answer.**

- *Check (before running): will turn 2's `input tokens` be bigger or smaller than turn 1's, and why?*
  *My answer:* "Bigger, as now history keeps accumulating and is fed as input." ✅ Exactly. Turn 2 re-sends turn 1's question **and** Claude's reply, plus the new question. Its input contains everything turn 1 had, plus more.

**Run result (2026-11-06):**

```text
Thanks for letting me know. Since you bought the jacket 20 days ago, you still have about 10 days left in our 30-day return window.
input tokens: 7

Yes, you can still return the jacket: it was bought 20 days ago, so about 10 days are left in our 30-day return window.
input tokens: 46
[PASS] Task history - Keep the conversation in a list
  Now the second answer knows the jacket and the days left. The memory of the conversation lives in your code: a list you send in full, every turn.
```

| Turn | Sent | Input tokens |
|---|---|---|
| 1 | user: jacket | **7** |
| 2 | user: jacket + **assistant: Claude's reply** + user: return it? | **46** |

Prediction confirmed: **7 → 46**. Most of the jump comes from **Claude's own first reply** (about 34 tokens) being sent back as input. The new question added only about 5.

> **Claude's answers become your next input. You pay for them again on every later turn.**

- *Check: if this chat went on for 200 turns, what two problems would this growing list cause, and what's one fix?*
  *My answer:* "It would cause filling of the context window. Summarization would fix it, but with the risk of losing exact details." ✅ One problem, the fix and its risk, all correct; but only one of two problems. The second starts much earlier: **cost and speed**. Every turn re-sends everything, so turn 200 pays for all 199 earlier turns again, and answers get slower. Quality can also drop before the window is full (facts lost in the middle).
  Fix for the risk named: keep the **exact facts in a separate "case facts" block** that's never summarized; summarize only the chatter (Q40, Q79).

  ```text
  SYSTEM + CASE FACTS {item: jacket, bought: 20 days ago, order: #1234}   ← kept exact
         + summary of old turns                                           ← compressed
         + last few turns                                                 ← verbatim
  ```

  > **Summarize the conversation, but never the facts.**
