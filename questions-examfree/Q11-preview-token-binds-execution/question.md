# Q11

*Source: practice set (pasted text).*

## Question

Your remove_team_member tool uses a dry_run: boolean parameter for previewing impacts before execution. Production monitoring shows the agent bypasses the preview step in 15% of calls by calling with dry_run=false directly. You need to ensure every removal is preceded by a preview that the user explicitly confirms. What is the most reliable approach?

- **A.** Add server-side validation that permits dry_run=false only when a dry_run=true call with identical parameters occurred within the past 60 seconds.
- **B.** Replace with two tools: preview_remove_member returns impact details and a single-use confirmation token; execute_remove_member requires that token, binding execution to the specific previewed action.
- **C.** Annotate the tool as requiring confirmation and configure the orchestration layer to prompt the user for approval before forwarding any calls to annotated tools.
- **D.** Add detailed instructions and few-shot examples to the tool description requiring the agent to always call with dry_run=true first and wait for user confirmation before calling with dry_run=false.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Make "preview first" impossible to skip: a preview tool issues a token, the execute tool requires it
**Domain / lesson:** D1 › 1.4 Workflow Enforcement & Handoff (a prerequisite gate in code)
**Also touches:** D2 › 2.1 Tool Interface Design (split one tool into two)
**Similar to:** [Q05](../../questions/Q05-goals-not-procedures-delegation/question.md) check (prerequisite gate), [Q17](../../questions/Q17-split-unified-tool/question.md), [Q28](../../questions/Q28-tamper-proof-threshold-in-tool/question.md) in the main set

## Answer: **B**

`dry_run: false` is just a flag the agent can set, and 15% of the time it does. In B, the remove tool **can't run without a token**, and only the preview tool hands one out:

```text
preview_remove_member("ana")   → "Ana loses access to 3 repos, 2 projects"  + token tk_91f
   (user sees the impact and says yes)
execute_remove_member("tk_91f") → removes Ana. The token is now used up.
```

- **Can't skip the preview:** no preview, no token.
- **Can't change the action:** the token belongs to *this* previewed removal of Ana, not to some other person.
- **Can't replay it:** the token works once.

| Option | Verdict | Why |
|---|---|---|
| **A** Allow `dry_run=false` only after a matching preview in the last 60 s | ❌ | Proves a preview **call** happened, not that the **user** saw and confirmed it. The agent can preview and execute in two quick calls. The 60-second window is also arbitrary: too short for a slow user, long enough for a skip. |
| **B** Preview tool issues a single-use token; execute tool requires it | ✅ | Code forces the order and ties the execution to exactly what was previewed. |
| **C** The orchestration layer asks the user before any call | ❌ | The user approves a **call**, not a **preview**. The agent can still call `dry_run=false` straight away, and the user is asked to confirm without seeing the impact. |
| **D** More instructions and few-shot examples | ❌ | Still just a request. Instructions are what's failing 15% of the time now; examples might lower that, not remove it. |

**My note on B:** the token proves the preview happened. To prove the **user** confirmed it, the token should be released by the user's own "confirm" action (e.g. a button in the app), not passed straight from preview to execute by the agent. The exam answer is still B: it's the only option that enforces the preview in code.

## Where it comes from

**Guide D1 › 1.4 Workflow Enforcement & Handoff** (as recorded in Q05, Q28):
- *"Prompt-based guidance is probabilistic. Programmatic enforcement is deterministic."* (**D**)
- *"A gate works every time. It is code, not a prompt instruction, so it is deterministic."* (**B**)
- *"if a single failure would cause financial loss, a security breach or a compliance violation, use programmatic enforcement."* Removing a team member changes access. (**B**)
- Trap: *"Enhanced system prompt instructions or few-shot examples as the fix for high-stakes compliance failures."* (**D**)

**Guide D2 › 2.1 Tool Interface Design** (as recorded in Q17):
- *"Split them into purpose-specific tools with defined input and output contracts."* (**B**: preview and execute become two tools)
- *"Few-shot examples add token overhead without addressing the root cause."* (**D**)

</details>
