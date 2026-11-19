# Q12

## Question

When two subagents at quicktechie.com provide conflicting statistical data from different credible sources during a synthesis task, what is the architecturally correct pattern for handling this uncertainty?

- **A.** Arbitrarily select the value from the most recently updated source to resolve the conflict smoothly.
- **B.** Silently average the conflicting numerical values to provide a balanced estimate to the user.
- **C.** Preserve both values and explicitly annotate the conflict with source attribution, letting the coordinator or user reconcile it.
- **D.** Drop the contested data point entirely to ensure the final report only contains unified facts.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Conflicting sources: keep both values, attribute them, flag the conflict
**Domain / lesson:** D5 › 5.6 Information Provenance & Multi-Source Synthesis
**Similar to:** [Q08](../../questions/Q08-conflicting-findings-annotation/question.md), [Q82](../../questions/Q82-dates-turn-conflicts-into-trends/question.md), [Q85](../../questions/Q85-render-each-content-type-appropriately/question.md) in the main set

## Answer: **C**

| Option | Verdict | Why |
|---|---|---|
| **A** Pick the most recent source | ❌ | Throws away the other credible value, so the reader never learns the sources disagree. The guide names this exact move as a trap. |
| **B** Silently average | ❌ | Produces a number **no source reported**, and "silently" hides that there was a conflict at all. |
| **C** Keep both, attribute, annotate | ✅ | Nothing is lost: both values, who said which, and a clear note that they conflict. The coordinator or user, who has the context, decides. |
| **D** Drop the data point | ❌ | Removes the information completely, and the report looks more certain than the evidence is. |

All three wrong options make the conflict disappear. The right one keeps it visible.

## Where it comes from

**Guide D5 › 5.6 Information Provenance & Multi-Source Synthesis** (as recorded in Q08, Q82, Q85):
- *"The correct approach is to annotate the conflict with both values and full source attribution, and let the consumer, who reads the output, decide."* (**C**)
- *"The analysis agent does not resolve the conflict, because the coordinator or the consumer makes that decision."* (**C**)
- Wrong approaches include picking one value by recency and averaging. *"Each of these choices destroys information and presents a false certainty."* (**A**, **B**, **D**)
- Exam trap: *"Selecting the most recent source when two credible sources conflict."* *"Arbitrarily selecting one value destroys information."* (**A**)

**Tip from Q82:** before calling it a conflict, check the dates. *"Different publication or data collection dates often explain different numbers."*

</details>
