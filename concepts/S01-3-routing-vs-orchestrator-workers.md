# Routing vs orchestrator-workers (and when to parallelize)

**Stage 1** of the [learning path](../study-map/learning-path.md) · **D1 › 1.2 Multi-Agent Orchestration** · **D1 › 1.6 Task Decomposition**
**Source of the "five core workflow patterns":** Anthropic, *Building effective agents* (prompt chaining, routing, parallelization, orchestrator-workers, evaluator-optimizer). The guide doesn't list the five by name. It teaches orchestrator-workers as **hub-and-spoke** (1.2) and **dynamic decomposition** (1.6).

## The question

> What is the actual difference between routing (a classifier sends input to a specialized downstream prompt) and orchestrator-workers (an orchestrator delegates to workers)?

## Short answer

**Routing picks one path. Orchestrator-workers splits the work into many parts.**

| | Routing | Orchestrator-workers |
|---|---|---|
| What the first step does | **Classifies** the input | **Breaks the task into** subtasks |
| How many handlers run | **One** (the chosen specialist) | **Several** workers |
| Who combines results | No one, because the one handler's answer is the answer | The orchestrator **merges** the workers' results |
| Are the subtasks known in advance? | Yes, a fixed set of routes | No, they're decided **at runtime** from the input |

- **Routing:** "Refund request?" goes to the refund prompt. "Tech issue?" goes to the support prompt. Only one runs.
- **Orchestrator-workers:** "Research X" becomes three searches plus one analysis, and then they're combined into one report.

## Follow-up: is orchestrator-workers less predictable? (2026-11-05)

**Yes. Of the five patterns, it's the least predictable.** The orchestrator decides the subtasks at runtime, so the number of workers, which workers run and how long it all takes can change for each input. In the other patterns, **your code** fixes the steps or routes in advance.

Guide 1.6 calls this a tradeoff:
- Dynamic decomposition *"generates subtasks from what each step discovers"*, and execution time *"varies with discoveries."*
- A fixed pipeline (prompt chaining) is the opposite. It's predictable, but it *"cannot adapt to unexpected findings."*

> **Use orchestrator-workers only when the subtasks can't be known in advance.** If you can list the steps beforehand, a fixed pattern (chaining, routing or parallelization) is the safer choice.

- *Check: for "Compare Q3 sales in Germany, France and Spain and suggest actions," are the subtasks known in advance, and which pattern fits?*
  *My answer:* "Yes, they are. A good candidate for parallelization?" ✅ The three countries are **known in advance** and **independent**, so they can run at the same time and code can combine the results. "Suggest actions" then becomes **one final step** over the combined results (a short chain after the parallel step).

- *Check: "Translate this document, then check the translation against a glossary and fix any mismatches until it passes." Which pattern is the "check and fix until it passes" part?*
  *My answer:* "Evaluator-optimizer?" ✅ One step **produces** the translation, and a second step **evaluates** it against a clear standard, the glossary. Any mismatches go back as feedback, and the loop repeats until the translation passes. It works well here because the **pass/fail criteria are explicit**. This is the same idea as validation/retry loops (guide 4.4).

- *Check: if the evaluator had only a vague standard, like "make it sound natural," what could go wrong with the loop?*
  *My answer:* "It might not pay attention to the content itself, like whether it's relevant to the question." 🟡 That's a real risk: the evaluator only checks what the criteria name, so content can slip through. The bigger problem, though, is with **the loop itself**:
  - **It may never stop.** "Natural" has no clear pass line, so the evaluator can always find something to tweak.
  - **Or it stops at random.** It might pass one version and fail an almost identical one.
  - **The feedback is vague.** "Sounds a bit stiff" doesn't tell the generator what to change.

  > **Evaluator-optimizer needs a clear pass/fail test.** Without one, the loop can't converge reliably. That's why the glossary example worked.

- *Check: how could you turn "make it sound natural" into a criterion the evaluator can actually pass or fail? Give one example.*
  *My answer:* "Something like 'Improve on parts X, justify claim Y, use live, natural everyday language when doing that'." 🟡 You're halfway there. "Improve part X" and "justify claim Y" are concrete, so the evaluator can check them. But "natural everyday language" is still vague. It's the same problem in different words.
  To make it **checkable**, name tests the evaluator can answer with yes or no:
  - Every sentence is 25 words or fewer.
  - There are no literal word-for-word idioms (for example, "it's raining cats and dogs" is translated by meaning, not word for word).
  - It uses the glossary's everyday terms, not technical synonyms.

  Or **show it**: give two or three example sentences labelled natural and not natural (few-shot, guide 4.2).

  > **A criterion is checkable when two evaluators would give the same pass/fail answer.**

## Pick the pattern

| Situation | Pattern |
|---|---|
| Fixed steps, each needs the previous one's output | **Prompt chaining** |
| One input, pick **one** specialist | **Routing** |
| Known, **independent** subtasks, run at the same time | **Parallelization** |
| Subtasks **unknown until runtime** | **Orchestrator-workers** (hub-and-spoke, 1.2) |
| Output must be checked and improved in a loop | **Evaluator-optimizer** |

> **Known steps → fixed pattern (predictable). Unknown steps → orchestrator-workers (adaptive, less predictable).**

## Sources

- Guide D1 › 1.2 Multi-Agent Orchestration (checked 2026-11-05): *"The exam tests one orchestration pattern: hub-and-spoke, with a coordinator at the centre."* *"The coordinator analyses what a query requires and selects only the subagents it needs."*
- Guide D1 › 1.6 Task Decomposition (checked 2026-11-05): *"A fixed sequential pipeline, also called prompt chaining, runs predetermined steps in order."* *"Prompt chaining suits predictable, structured tasks."* *"Dynamic adaptive decomposition generates subtasks from what each step discovers."*
