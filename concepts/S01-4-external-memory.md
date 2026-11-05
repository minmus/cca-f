# External memory for agents

**Stage 1** of the [learning path](../study-map/learning-path.md) (Udemy A #21 In-Context State vs. External Memory) · **D5 › 5.1 Context Window Management** · **D1 › 1.7 Session State & Resumption**
**Builds on:** [S01-2-context-window-across-sessions](S01-2-context-window-across-sessions.md) (the model forgets everything after each request)

## The question

> In simple terms, one idea at a time: how does external memory work with agents? Relational or index databases?

## Idea 1: external memory is storage outside the context window, reached through tools

The model forgets everything after each request (S01-2). External memory is a place **outside the model** where facts are kept between requests and sessions: a database, a file or a search index.

The agent **can't see it directly**. It uses it like any other tool:

```text
Agent: "I need the customer's past orders"
  → tool_use: query_orders(customer_id=42)
  ← tool_result: [order 1, order 2]     ← only THIS enters the context window
```

- **Writing:** the agent calls a tool such as `save_note(...)` and the fact goes into the database.
- **Reading:** the agent calls a tool such as `search_notes(...)`, and **only the matching results** go into the context window.

> **The database can hold millions of facts. The context window only gets the few the agent asks for.**

- *Check: why not just paste the whole database into the prompt on every request?*
  *My answer:* "It would overload the context window with details that are not relevant to the request." ✅ Correct. There are two more reasons:
  - **It often won't fit at all.** A database can be far bigger than any context window.
  - **Even when it fits, quality drops.** The model's attention gets spread over irrelevant text, and facts in the middle get missed (the "lost in the middle" effect, guide 5.1).

  > **Retrieve only what this request needs.**

## Next idea (to do)

Relational database vs index (vector search), and when each fits.
