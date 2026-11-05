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

## Idea 2: a relational database finds exact matches, and a vector index finds similar meaning

| | Relational database (SQL) | Vector index (semantic search) |
|---|---|---|
| Stores | Rows with fields: `customer_id`, `order_date`, `status` | Text turned into **embeddings**, which are lists of numbers that capture its meaning |
| Agent asks | "Orders where `customer_id = 42` and `status = 'open'`" | "Notes **about** refund problems" |
| Finds | **Exact** matches only | **Similar** text, even with different words ("money back" matches "refund") |
| Good for | Facts with known keys: IDs, dates, amounts, statuses | Fuzzy questions over documents, tickets, past conversations |

> **You know the exact key → relational. You only know roughly what it's about → vector index.**

In both cases the agent reaches the data through a **tool** (idea 1), and only the results enter the context window.

- *Check: the agent needs "the refund amount on order #1234." Which store fits, and why?*
  *My answer:* "Relational database" ✅ Right store, but the *why* was missing. You have an **exact key**, order #1234, and you need an **exact number**. A relational query returns exactly that row. A vector search could return a **similar** order instead, like #1243, or a note that only talks about refunds. "Close enough" is wrong for an amount.

- *Check: the agent needs "past tickets where customers complained the app was slow." Which store fits, and why?*
  *My answer:* "Vector index" ✅ Right store, but the *why* was missing again. There's **no exact key**, and customers describe slowness in **different words**, like "laggy," "takes forever" or "freezes." A SQL `WHERE text = 'slow'` would miss most of those. A vector index matches on **meaning**, so it finds them all.
  *Exam tip:* always add "because…". The answer options are often traps where the store is right but the reason is wrong.

## Idea 2b: how a vector index is built (a map where similar meanings sit close together)

**Building it (done once, ahead of time):**
1. **Split** the documents into small chunks, for example one ticket or one paragraph each.
2. An **embedding model** turns each chunk into a list of numbers, the *embedding*. Treat that list as **coordinates on a map of meaning**.
3. **Store** each chunk's coordinates together with its original text.

```text
"App is laggy"          → [0.81, 0.12, …]  ┐  close together on the map
"Takes forever to load" → [0.79, 0.15, …]  ┘  (similar meaning)
"Refund my order"       → [0.05, 0.93, …]     far away (different topic)
```

**Searching it (every query):**
1. Turn the **question** into coordinates with the **same** embedding model.
2. Find the chunks whose coordinates are **nearest**.
3. Return **their original text**, which is what goes into the context window.

> **Same meaning → nearby numbers. Search means "find the nearest neighbours to my question."**

- *Check: why must the question be embedded with the same model that built the index?*
  *My answer:* "So that common calibrated values are compared when searching?" ✅ Yes, that's the idea. Each embedding model draws **its own map of meaning**. If you embed the question with a different model, its coordinates belong to **another map**. The "nearest" chunks are then just random results, because the numbers aren't measured the same way. It's like looking up GPS coordinates on a city map that uses its own grid: the numbers exist, but they point to the wrong place.

  > **Index and query must use the same embedding model. Change the model, and you must rebuild the index.**
