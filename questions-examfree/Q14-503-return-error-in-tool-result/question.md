# Q14

*Source: practice set (pasted text).*

## Question

Your search Flights tool calls an external airline API that occasionally returns a 503 Service Unavailable error. What is the most effective way to handle this error in your tool implementation?

- **A.** Return an empty flight list as if the search succeeded but found no matching flights.
- **B.** Log the error internally and return an empty response, letting the model continue without the flight data.
- **C.** Return an error message in the tool result explaining the service is temporarily unavailable.
- **D.** Automatically retry the request up to five times with exponential backoff before returning results to the agent.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** API down (503): tell the model in the tool result, don't hide it
**Domain / lesson:** D2 › 2.2 Structured Error Responses
**Similar to:** [Q24](../../questions/Q24-structured-errors-not-exceptions/question.md), [Q25](../../questions/Q25-mcp-protocol-vs-tool-errors/question.md) in the main set · [Q16](../Q16-access-failure-vs-valid-empty-result/question.md) in this folder

## Answer: **C**

A 503 means the search **never happened**. The model has to know that, so it can retry later or tell the user "flight search is down right now". In practice: `isError: true` plus a clear message, ideally tagged as a transient, retryable error.

| Option | Verdict | Why |
|---|---|---|
| **A** Empty list, as if nothing matched | ❌ | A lie: the model tells the user "no flights found" when the search never ran. This is the access-failure vs empty-result trap. |
| **B** Log it, return an empty response | ❌ | Same problem, hidden in a log only developers read. The model can't recover from a failure it can't see. |
| **C** Error message in the tool result | ✅ | The model sees what happened and can act: retry, try later, or tell the user honestly. |
| **D** Retry 5× with exponential backoff | ❌ | A few retries inside the tool are fine **as an addition** for a temporary error. But on its own D doesn't say what the model gets if all five fail, and five retries with growing waits can add many seconds of delay. The model still needs C's message in the end. |

## Where it comes from

**Guide D2 › 2.2 Structured Error Responses** (read 2026-11-14, and as recorded in Q22, Q24, Q32):
- *"When the tool sets `isError`, the model knows that the execution failed."* (**C**)
- Exam trap: *"Silently suppressing subagent errors by returning empty results as success."* (**A**, **B**)
- *"Only transient errors get [`isRetryable`] `true`."* A 503 is transient. (**C**: say so in the error)
- The guide's retry example: *"retries transient errors up to 3 times with backoff."* Bounded retries are an addition, then report. (**D**)

**MCP specification, Tools › Error Handling** (as recorded in Q25):
- Tool execution errors, reported *"in tool results with `isError: true`"*, include *"API failures"*. (**C**)
- *"Tool Execution Errors contain actionable feedback that language models can use to self-correct and retry with adjusted parameters."* (**C**)

**Claude docs, Handle tool calls** (read 2026-11-14):
- *"`is_error` (optional): Set to `true` if the tool execution resulted in an error."* (**C**)
- *"Write instructive error messages. Instead of generic errors such as `"failed"`, include what went wrong and what Claude should try next."* (**C**)

</details>
