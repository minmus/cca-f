# Q20

*Source: practice set (pasted text).*

## Question

Your publish article tool calls an external CMS API that occasionally returns transient errors (network timeouts, 503s) and non-transient errors (403 permission denied, 422 validation failure).

Currently, every error is returned directly to the agent, which leads to the agent retrying non-transient errors and wasting turns on failures that will never succeed. How should you partition error-handling responsibility between the tool implementation and the agent?

- **A.** Handle all errors inside the tool: Implement retries with exponential backoff for every error type, and only surface a failure to the agent after a fixed number of retry attempts have been exhausted.
- **B.** Handle transient errors (timeouts, 503s) with automatic retries inside the tool implementation, and surface non-transient errors (permission denied, validation failures) to the agent with descriptive messages so it can take corrective action.
- **C.** Surface all errors to the agent immediately with detailed context, and let the agent decide which errors to retry and how many times, keeping the tool implementation stateless and simple.
- **D.** Implement a universal error handler that catches all exceptions and returns a generic "tool unavailable, try again later" message, shielding the agent from error complexity.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Split the work: the tool retries temporary errors, the agent handles the rest
**Domain / lesson:** D2 › 2.2 Structured Error Responses
**Also touches:** D5 › 5.3 Error Propagation (recover locally first)
**Similar to:** [Q32](../../questions/Q32-uniform-errors-add-category/question.md), [Q34](../../questions/Q34-business-errors-not-retryable/question.md), [Q67](../../questions/Q67-subagent-timeout-structured-error-context/question.md) in the main set · [Q14](../Q14-503-return-error-in-tool-result/question.md) in this folder

## Answer: **B**

Each error goes to whoever can fix it:

| Error | Will retrying help? | Who handles it |
|---|---|---|
| Timeout, 503 | ✅ Often, a moment later | **Tool code**: a few retries with backoff, no model turns spent |
| 403 permission denied | ❌ Never | **Agent**: e.g. ask for access or escalate |
| 422 validation failure | ❌ Not with the same input | **Agent**: fix the input and try again |

| Option | Verdict | Why |
|---|---|---|
| **A** Retry every error inside the tool | ❌ | Retrying 403 and 422 is pure waste; they fail the same way every time. It just moves the bug into code and adds delay. |
| **B** Tool retries transient; agent gets the rest, described | ✅ | Temporary errors are fixed cheaply in code. Permanent ones reach the agent with enough detail to act. |
| **C** Everything to the agent | ❌ | That's roughly today's setup. Simple retries cost model turns that code could do for free, and every agent has to re-learn which errors to retry. |
| **D** One generic "try again later" | ❌ | The worst option: it tells the agent to retry errors that can **never** succeed, and hides what actually went wrong. |

**Caveat for this tool** (my note, see [Q26](../../questions/Q26-timeout-unknown-status-no-retry/question.md) in the main set): `publish_article` changes things. After a **timeout** you don't know whether the article was published, so a blind retry could publish it twice. Retry only if the CMS supports an idempotency key, or check first.

## Where it comes from

**Guide D2 › 2.2 Structured Error Responses** (read 2026-11-14, and as recorded in Q32, Q34):
- The guide's retry example: *"retries transient errors up to 3 times with backoff."* (**B**)
- *"Only transient errors get [`isRetryable`] `true`."* (**A**)
- *"Without errorCategory, isRetryable, and a description, the agent cannot distinguish transient failures from business rule violations."* (**B**, **D**)
- Exam traps: *"Marking a validation error isRetryable: true because the agent can recover from it"* (**A**) · *"Using generic error messages like 'Operation failed' without structured metadata"* (**D**)

**Guide D5 › 5.3 Error Propagation** (as recorded in Q67):
- *"Subagents should recover locally from transient failures (retry logic, fallback sources, lower-quality responses) before propagating errors."* (**B**, **C**)

</details>
