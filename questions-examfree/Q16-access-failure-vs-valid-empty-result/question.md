# Q16

## Question

When designing an agent to interact with a backend database, it is critical to distinguish between access failures and valid empty results. Which of the following statements correctly describe handling these scenarios? Choose 2 correct answers.

- **A.** An access failure (e.g., database timeout) should return isError: true so the agent knows the check was not actually performed.
- **B.** An access failure should silently return an empty array [] to smoothly transition to alternative tools.
- **C.** A valid empty result (searched successfully, but no matches) should return isError: false.
- **D.** Both access failures and empty results should throw fatal exceptions to the coordinator to halt execution.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Access failure (`isError: true`) vs valid empty result (`isError: false`)
**Domain / lesson:** D2 › 2.2 Structured Error Responses
**Also touches:** D5 › 5.3 Error Propagation (D)
**Similar to:** [Q22](../../questions/Q22-empty-result-is-not-error/question.md), [Q67](../../questions/Q67-subagent-timeout-structured-error-context/question.md) in the main set

## Answer: **A, C**

| Option | Verdict | Why |
|---|---|---|
| **A** Access failure → `isError: true` | ✅ | The search never happened. `isError` tells the agent that, so it can retry or try another way instead of trusting an answer. |
| **B** Access failure → silent `[]` | ❌ | An empty list says "I searched and found nothing". That's a lie here, and the agent would report "no matches" as fact. The guide names this as a trap. |
| **C** Valid empty result → `isError: false` | ✅ | The search worked. "Nothing found" **is** the answer, and marking it as an error would make the agent retry pointlessly. |
| **D** Both → fatal exception, halt | ❌ | An empty result isn't a failure at all. And halting everything over one failure throws away work that succeeded and leaves no chance to recover. |

| | The search ran? | Return | Agent should |
|---|---|---|---|
| Access failure (timeout) | ❌ No | `isError: true` | Retry or try another approach |
| Valid empty result | ✅ Yes | `isError: false`, e.g. `resultCount: 0` | Accept "nothing found" |

## Where it comes from

**Guide D2 › 2.2 Structured Error Responses** (read 2026-11-14, and as recorded in Q22):
- *"The key distinction to learn in this domain is between an access failure and a valid empty result. The exam tests it directly."*
- A valid empty result means the tool *"queried the data source successfully and found no matches"*. *"Retrying will produce the same empty result."* (**C**)
- An access failure means the tool *"could not reach the data source."* (**A**)
- *"When the tool sets `isError`, the model knows that the execution failed."* (**A**)
- The lesson's examples: the valid empty result returns `"isError": false` with `"structuredContent": {"resultCount": 0}`. The access failure returns `"isError": true`. (**A**, **C**)
- Exam traps: *"Retrying when a tool returns an empty result from a successful query"* (**C**) · *"Silently suppressing subagent errors by returning empty results as success"* (**B**)

**Guide D5 › 5.3 Error Propagation** (as recorded in Q67):
- *"One subagent times out and the whole pipeline crashes. The other four completed successfully, but their results are discarded."* (**D**)

</details>
