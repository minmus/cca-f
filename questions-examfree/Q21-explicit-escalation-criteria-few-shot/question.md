# Q21

## Question

Your agent achieves a low first-contact resolution rate because it attempts to autonomously handle complex policy exceptions, while simultaneously escalating straightforward tasks. What is the most effective approach to improve this escalation calibration?

- **A.** Add explicit escalation criteria to the system prompt with few-shot examples demonstrating when to escalate versus resolve autonomously.
- **B.** Deploy a separate classifier model to predict which requests need escalation before the main agent begins.
- **C.** Require the agent to self-report a confidence score (1–10) and route to humans when confidence falls below 7.
- **D.** Implement sentiment analysis to detect frustration levels and automatically escalate when they are high.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Escalating the wrong cases: explicit criteria + few-shot examples
**Domain / lesson:** D5 › 5.2 Escalation & Ambiguity Resolution
**Also touches:** D4 › 4.1 Explicit Criteria · D4 › 4.2 Few-Shot
**Similar to:** [Q16](../../questions/Q16-escalation-triggers/question.md), [Q43](../../questions/Q43-frustration-offer-resolve-or-escalate/question.md) in the main set

## Answer: **A**

The agent gets it **backwards**: it keeps policy exceptions (which need a human) and escalates easy tasks (which it could solve). It simply hasn't been told where the line is.

| Option | Verdict | Why |
|---|---|---|
| **A** Explicit criteria + few-shot examples | ✅ | The criteria say **when** to escalate (e.g. a policy exception → escalate). The examples show borderline cases on each side of the line. This fixes both mistakes at the root. |
| **B** A separate classifier first | ❌ | Heavy machinery as a first step: you'd need labelled data and a new model, and it decides before the conversation shows what the case really is. The simple fix (clear criteria) hasn't been tried yet. (Reasoning, not a guide quote.) |
| **C** Self-reported confidence < 7 → human | ❌ | The model's confidence about itself isn't reliable. It can be confident while handling a policy exception it shouldn't touch. |
| **D** Escalate on high frustration | ❌ | Frustration doesn't tell you how complex a case is. An angry customer with an easy issue should get a fix, not a handoff. |

## Where it comes from

**Guide D5 › 5.2 Escalation & Ambiguity Resolution** (as recorded in Q16, Q43):
- Valid triggers: *"The customer explicitly requests a human."* · *"The request is outside documented policy."* · *"The agent has tried and cannot advance."* (**A**: these are the criteria to write down)
- *"LLM self-reported confidence is poorly calibrated."* (**C**)
- *"Frustration does not correlate with case complexity."* *"Frustration alone does not decide the response."* (**D**)

**Guide D4 › 4.2 Few-Shot** (as recorded in Q112, Q121):
- *"Choose examples that cover the specific ambiguous cases that cause problems."* (**A**)
- *"These ambiguous cases need examples that show the correct judgement, with reasoning."* (**A**)

</details>
