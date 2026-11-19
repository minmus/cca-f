# Q09

## Question

The coordinator provides detailed step-by-step instructions to the web search subagent, specifying exact search queries, source priorities, and date filters. Production monitoring reveals three issues:

1. the subagent reports "insufficient results" rather than trying alternative approaches when pre-specified searches fail,
2. research quality drops for emerging topics that don't match expected patterns,
3. the subagent rarely surfaces valuable tangential sources.

What's the most effective way to improve subagent adaptability?

- **A.** Implement a topic classification step where the coordinator categorizes requests as "well-defined" or "exploratory" and uses different instruction styles for each category.
- **B.** Add explicit fallback directives to the detailed instructions: "If specified searches yield fewer than N results, attempt alternative query formulations before reporting failure."
- **C.** Remove procedural details entirely, delegating with simple goals like "research X thoroughly" and relying on the subagent's general capabilities.
- **D.** Specify research goals and quality criteria (coverage breadth, source diversity, recency) rather than procedural steps, letting the subagent determine its search strategy.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Delegate goals and quality criteria, not step-by-step procedures
**Domain / lesson:** D1 › 1.3 Subagent Invocation and Context Passing
**Same question as:** [Q05](../../questions/Q05-goals-not-procedures-delegation/question.md) in the main set

## Answer: **D**

| Option | Verdict | Why |
|---|---|---|
| **A** Classify as well-defined vs exploratory | ❌ | Adds a step that can misclassify, and keeps the procedural style for "well-defined" requests, where issues 1 and 3 still happen. |
| **B** Add fallback directives | ❌ | Fixes issue 1 only. It's still a procedure, just a longer one, and you can't write a fallback for every failure. Issues 2 and 3 remain. |
| **C** Just "research X thoroughly" | ❌ | Goes too far: it drops the procedure **and** the quality criteria. The subagent sees only its prompt, so it has no standard for "done" or "good". |
| **D** Goals + quality criteria, subagent picks the strategy | ✅ | Fixes all three. A failed search doesn't meet the goal yet, so it tries something else (1). It chooses its own strategy for new topics (2). "Source diversity" rewards tangential sources (3). |

## Where it comes from

**Guide D1 › 1.3 Subagent Invocation and Context Passing** (as recorded in Q05):
- *"Specify goals, not procedures."* *"A coordinator prompt should tell a subagent what to achieve and which quality criteria to meet."* (**D**)
- Exam trap: a fixed procedure *"breaks when the input differs from what its author expected."* (**A**, **B**)
- *"A subagent receives only what the coordinator writes into the subagent's prompt."* (**C**)

</details>
