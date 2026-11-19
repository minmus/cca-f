# Q19

*Source: practice set (pasted text).*

## Question

Your agent has access to 50+ specialized API connectors for different external services. As the connector library grew, tool selection accuracy dropped to 58%. You design a search_connectors(description) tool that finds matching connectors, but in testing agents frequently skip searching and call connectors directly (often incorrectly), or search select wrong connectors from the filtered results.

How should you design the tool composition pattern to address both issues?

- **A.** Design connectors with built-in compatibility validation that return descriptive errors for mismatched requests.
- **B.** Design a find_and_execute(description, params) composite tool that searches and immediately executes the best matching connector.
- **C.** Enhance all connector descriptions with detailed usage samples, edge cases, and input requirements. Add few-shot examples showing the correct search-then-use workflow.
- **D.** Design search_connectors to dynamically add matched connectors to the agent's available tools. Connectors start unavailable and persist once discovered.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Too many tools: hide them until a search makes them available
**Domain / lesson:** D2 › 2.3 Tool Distribution & Tool Choice
**Similar to:** [Q47](../../questions/Q47-merge-overlapping-tools/question.md), [Q04](../../questions/Q04-tool-scoping-per-subagent/question.md) in the main set

## Answer: **D**

D fixes both problems with **structure**, not instructions:
1. **Can't skip the search:** connectors aren't in the agent's tool list until a search adds them, so there's nothing to call directly.
2. **Picks better:** the agent now chooses among the few connectors the search returned, not 50+. Discovered connectors stay, so it doesn't have to search again.

This is how Claude's own **tool search tool** works (`defer_loading: true`).

| Option | Verdict | Why |
|---|---|---|
| **A** Connectors return errors on wrong requests | ❌ | Catches mistakes **after** the wrong call. All 50+ connectors stay visible, so the agent keeps skipping the search. |
| **B** `find_and_execute` runs the best match at once | ❌ | Takes the choice away from the model and runs whatever matched, even when the match is wrong. The agent also has to supply `params` before it knows which connector, and what inputs it needs. |
| **C** Longer descriptions + few-shot examples | ❌ | More tokens on top of 50+ definitions, and it's still just a request: the agent **can** still skip the search. Better descriptions help with a workable number of tools, not 50+. |
| **D** Search adds connectors; they start hidden and stay once found | ✅ | Makes searching first unavoidable and keeps the choice small. |

## Where it comes from

**Claude docs, Tool search tool** (read 2026-11-19):
- *"The tool search tool lets Claude work with hundreds or thousands of tools by discovering and loading them on demand."* (**D**)
- *"Claude's ability to pick the right tool degrades once you exceed 30–50 available tools."* *"Because tool search loads only a focused set of relevant tools on demand, selection accuracy stays high even across thousands of tools."* (**D**)
- *"Tools with `defer_loading: true` load only when Claude discovers them through search."* (**D**: start unavailable)
- *"The API expands `tool_reference` blocks throughout the conversation history, so Claude can reuse discovered tools in later turns without re-searching."* (**D**: persist once discovered)

**Guide D2 › 2.3 Tool Distribution & Tool Choice** (as recorded in Q04, Q47):
- *"Every additional tool makes the decision more complex, so error rates increase as the toolkit grows."*
- *"When you give a single agent 18 tools, the agent selects tools less reliably."*
- *"Anthropic's tool search docs say Claude's ability to pick the right tool degrades once you exceed 30 to 50 available tools."* (**D**)

**Guide D2 › 2.1 Tool Interface Design** (as recorded in Q47):
- *"Descriptions are the fix when the agent has a workable number of tools and cannot distinguish two of them."* (**C**: 50+ isn't workable)
- *"Few-shot examples add token overhead without addressing the root cause."* (**C**)

</details>
