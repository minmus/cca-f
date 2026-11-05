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

## Idea 2c: where the numbers come from (the embedding model learned them from examples)

An embedding model is a neural network **trained on huge numbers of text pairs**:
- Pairs that **mean the same thing**, like "app is slow" and "takes forever to load," are pushed **closer together**.
- Pairs that **don't**, like "app is slow" and "refund my order," are pushed **further apart**.

After enough training, the model has learned **where on the map to put any text**, including text it has never seen.

```text
"app is slow"  → model → [0.81, 0.12, -0.40, …]   ← typically hundreds to thousands of numbers
```

No single number means anything on its own, the way "number 7 = speed" would. **Together** they place the text on the map. Nobody hand-picks them, because the training sets them.

> **Embedding = a trained model's answer to "where does this text belong on the map of meaning?"**

*Exam relevance: low. The CCA-F exam doesn't test how embeddings work inside. This is background only.*

- *Check: two tickets use completely different words but describe the same problem. Why do they still end up close on the map?*
  *My answer:* "Close, because the embedding model is supposed to place them close together." 🟡 That's the right outcome, but "is supposed to" doesn't say **why** it can. The reason is training: the model saw huge numbers of pairs with **different words and the same meaning**, and it was pushed to place each pair close together. So it learned to place text by **meaning, not by the words used**. That's exactly why a vector index finds "laggy" when you search for "slow," and a SQL word match doesn't.

## Idea 2d: how "nearest" is measured (compare the direction of the arrows)

Picture each embedding as an **arrow** starting from the centre of the map. Texts with similar meaning have arrows pointing in **almost the same direction**.

The usual measure is **cosine similarity**, which asks how closely two arrows point the same way:

| Arrows | Score | Meaning |
|---|---|---|
| Same direction | **≈ 1** | Very similar meaning |
| At a right angle | **≈ 0** | Unrelated |
| Opposite directions | **≈ −1** | Opposite (rare in practice) |

```text
"app is slow"           → [0.8, 0.2]  ┐ almost the same direction → score ≈ 0.99
"takes forever to load" → [0.7, 0.3]  ┘
"refund my order"       → [0.1, 0.9]    different direction       → score ≈ 0.35
```

**Search** compares your question's arrow with every stored arrow and returns the **top few scores**, for example the top 5.

> **Nearest = the highest similarity score = arrows pointing the most alike.**

*Exam relevance: low. This is background only.*

- *Check: the search returns the top 5 results, but even the best score is only 0.3. What does that tell you?*
  *My answer:* "That means their vectors are pointing in different directions, and thus the meanings are not too similar." ✅ Correct. Even the "best" matches aren't really about your question.
  One practical trap follows from this: **top-5 always returns 5 results, even when nothing relevant exists.** If you pass them along unchecked, the agent may treat weak matches as an answer and build a confident reply on unrelated text. The fix: set a **minimum score**, for example "ignore anything below 0.7." If nothing passes, the tool returns "no relevant results," and the agent says so or searches differently.

  > **"Top results" doesn't mean "good results." Check the score before trusting a match.**

- *Check: the agent's search tool returns 5 results that all score below 0.7, your minimum. What should the tool send back to the agent, and why?*
  *My answer:* "The scores and values, so that the agent could decide if this is remotely related or not." 🟡 It's reasonable to send the scores back, but that undoes the point of the threshold. You set 0.7 so that **code** makes this decision. If weak results go back anyway, you're relying on the agent to resist using text that sits right in front of it, which is the probabilistic part again.
  Better: the tool returns a **clear, structured "nothing found"**:

  ```json
  {"status": "no_relevant_results", "best_score": 0.3, "threshold": 0.7}
  ```

  - The agent **knows** the search worked and found nothing relevant. That's different from a failed search (guide 2.2: an empty result is not an error).
  - It can **rephrase and search again**, or tell the user it doesn't know.
  - It never sees the weak text, so it can't build an answer on it.

  > **A threshold decides in code. Return "nothing relevant," not weak matches.** (This is the same idea as [Q21](../questions/Q21-review-flag-from-calibrated-thresholds/question.md): thresholds live in code, not in model judgment.)

## Idea 3: who decides what gets saved to memory, and when?

| | **The agent decides** | **Your code decides** |
|---|---|---|
| How | The agent calls a tool such as `save_note("customer prefers email")` whenever it thinks something is worth keeping | Code saves at fixed points: after every tool result, at the end of each session, or when a field is extracted |
| Good for | Flexible notes and insights you can't predict | Facts that **must** be kept: IDs, amounts, decisions |
| Risk | It may **forget** to save something important | Less flexible |

> **If it's nice to remember, the agent can decide. If it must be remembered, code saves it.**

It's the same rule as before: guarantees come from **code**, not from the model's judgment (Q50, Q28).

- *Check: a support agent learns that the customer's refund was approved for €120. Who should save that fact, and why?*
  *My answer:* "Code. To be sure." ✅ Correct, with a why. To sharpen "to be sure," here are the two failure modes code prevents:
  - **The agent might skip the save**, because saving is only a probability when the agent decides.
  - **The agent might change the value** while restating it, for example writing "about €120," or €210.

  Code takes the amount **straight from the tool result** (`refund_approved: 120.00`) and saves it unchanged.
