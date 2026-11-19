# Q36

*Source: practice set (pasted text). Option C is garbled in the source and kept as given.*

## Question

Your schema includes a skills: string[] field. Production monitoring reveals three consistency issues: (1) compound phrases like "Python and SQL" are sometimes kept as one entry, sometimes split; (2) implied but unstated skills occasionally appear in extractions; (3) similar documents produce wildly different array lengths (5-10 vs 40+ entries). Your prompt currently says "Extract skills mentioned." What's the most effective improvement?

- **A.** Add constraints: "Extract 10-20 skills maximum, one skill per entry, only explicitly named skills."
- **B.** Add post-extraction normalization that maps skills to a canonical taxonomy and deduplicates similar entries.
- **C.** Enrich the schema to [scondidering] to capture extraction metadata.
- **D.** Add few-shot examples demonstrating compound phrase handling, explicit mention criteria, and appropriate entry granularity.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Inconsistent extraction: show worked examples, not more rules
**Domain / lesson:** D4 › 4.2 Few-Shot
**Similar to:** [Q112](../../questions/Q112-few-shot-standard-materials-format/question.md) in the main set · [Q06](../Q06-few-shot-2-to-4-ambiguous-cases/question.md) in this folder

## Answer: **D**

All three problems are **judgment calls** that "Extract skills mentioned" leaves open. One worked example settles each of them:

```text
Text:  "Built ETL pipelines in Python and SQL; led a team of 4."
Skills: ["Python", "SQL", "ETL"]
Why:   "Python and SQL" is two skills → split.
       "Leadership" is only implied → leave it out.
       "pipelines" isn't a separate skill from ETL → one entry.
```

| Option | Fixes (1) compounds? | (2) implied skills? | (3) array length? | Why |
|---|---|---|---|---|
| **A** "10–20 max, one per entry, explicit only" | partly | partly | ❌ forces it | Rules help a little, but "10–20" is arbitrary: a CV with 6 skills gets padded to 10 (more invented skills), and one with 30 gets cut. |
| **B** Normalize to a taxonomy afterwards | ✅ | ❌ | partly | Cleans up wording **after** extraction. It can't tell an implied skill from a stated one, because it never sees the text. |
| **C** Extra metadata in the schema | ❌ | ❌ | ❌ | (Garbled.) Metadata describes the extraction; it doesn't make it consistent. |
| **D** Few-shot examples with reasoning | ✅ | ✅ | ✅ | Shows the model where the lines are, so it applies them the same way every time. |

## Where it comes from

**Guide D4 › 4.2 Few-Shot** (as recorded in main-set Q112):
- *"Few-shot examples are the most effective technique for consistent, well-formatted output from Claude. More instructions, confidence thresholds and temperature adjustments all do less. When output is inconsistent, use few-shot examples first."* (**D**, **A**)
- *"Choose examples that cover the specific ambiguous cases that cause problems."* (**D**: compound phrases, implied skills)
- *"Each example must show reasoning."* *"The reasoning teaches the model to generalise its judgement to novel patterns, instead of only matching the cases in your examples."* (**D**)
- *"Use 2 to 4 targeted examples."*

**Guide D4 › 4.3** (as recorded in main-set Q112): *"A required field pressures the model to produce a value even when the source has none."* The same goes for a minimum count: "at least 10" pressures it to invent. (**A**)

</details>
