# Q26

## Question

When the agent calls lookup_order and receives order details showing the item was purchased 45 days ago, how does the agentic loop determine whether to call process_refund or escalate_to_human next?

- **A.** The orchestration layer automatically routes to the next tool based on the order's status field.
- **B.** The order details are added to the conversation and the model reasons about which action to take.
- **C.** The agent executes the remaining steps in a tool sequence planned at the start of the request.
- **D.** The agent follows a pre-configured decision tree mapping order attributes to specific tool calls.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** In an agentic loop, the model picks the next tool from the updated conversation
**Domain / lesson:** D1 › 1.1 Agentic Loops
**Same question as:** [Q13](../../questions/Q13-agentic-loop-model-decides-next-tool/question.md) in the main set

## Answer: **B**

The loop: your code runs `lookup_order`, adds the result ("bought 45 days ago") to the conversation, and sends it back to Claude. Claude reads it and decides: refund or escalate. Your code runs tools; it doesn't choose them.

| Option | Verdict | Why |
|---|---|---|
| **A** Code routes by the status field | ❌ | That's a hard-coded router. In an agentic loop the code only runs tools and passes results back. |
| **B** Result added to the conversation, model decides | ✅ | This is how the agentic loop works: each next step is decided **after** seeing the latest result. |
| **C** Follow a sequence planned at the start | ❌ | A plan made before seeing the order can't react to "45 days". |
| **D** Pre-configured decision tree | ❌ | That's a fixed **workflow**, where the developer decides which tool runs when. It's valid for some systems, but it isn't an agentic loop. |

**Nuance:** the model **chooses** the next step, but a rule that must always hold (e.g. "no refunds after 30 days") is still **enforced in code** inside the refund tool.

## Where it comes from

**Guide D1 › 1.1 Agentic Loops** (as recorded in Q13):
- *"Your code must append the tool results to the conversation history."* Otherwise *"the model never sees what the tool returned."* (**B**)
- *"Claude uses the current context to decide which tool to call."* (**B**)
- A pre-configured workflow is where *"the developer hard-codes which tool runs at which point."* (**A**, **C**, **D**)
- For rules that need deterministic compliance, *"programmatic enforcement overrides that flexibility."* (the nuance)

</details>
