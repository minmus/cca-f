# Q32

*Source: practice set (pasted text). Garbled words in the paste are corrected here [in brackets].*

## Question

Your invoice extraction uses tool use with strict JSON schemas. JSON syntax errors never occur, but 12% of extractions fail semantic validation, for example, line item amounts don't [sum to the] extracted total, or vendor IDs don't match valid formats. These failures currently route to manual review. What's the most effective approach to reduce manual review volume while m[aintaining] accuracy?

- **A.** Retry the extraction up to 3 times when validation fails, accepting the first result that passes validation.
- **B.** Implement post-processing logic that automatically corrects common [errors], such as recalculating totals from line items when sums don't match.
- **C.** When validation fails, make a follow-up request with the document, extraction, and validation errors for model correction.
- **D.** Add stricter schema constraints with detailed field descriptions to prevent the model from generating invalid values initially.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Semantic errors: send the document, the bad extraction and the exact error back for correction
**Domain / lesson:** D4 › 4.4 Validation, Retry & Feedback
**Similar to:** [Q113](../../questions/Q113-few-shot-varied-document-structures/question.md), [Q114](../../questions/Q114-calibrated-field-confidence-routes-review/question.md) in the main set · [Q29](../Q29-schema-guarantees-structure-not-meaning/question.md), [Q31](../Q31-retries-cant-fetch-missing-information/question.md) in this folder

## Answer: **C**

The model gets everything it needs to fix its own mistake:

```text
Here is the invoice:                    [document]
You extracted:                          {"line_items": [400, 250], "total": 750}
Validation failed:                      line items sum to 650, but total is 750.
                                        Re-check the line items against the document.
```

Re-reading the invoice, it finds the third line item (100) it missed. The fix comes **from the document**, so accuracy is kept, and that case no longer goes to manual review.

| Option | Verdict | Why |
|---|---|---|
| **A** Retry up to 3× without the error | ❌ | A blind retry: the model doesn't know what was wrong, so it usually repeats it. And "accept the first that passes" can accept a **different** wrong answer that happens to add up. |
| **B** Auto-correct, e.g. recalculate the total | ❌ | Code guesses **which** number is wrong. Above, the total (750) was right and a line item was missing; recalculating would make the total wrong (650) and hide the mismatch. Fewer reviews, worse accuracy. |
| **C** Follow-up with document + extraction + errors | ✅ | The guide's retry pattern. Fixes are checked against the source, not guessed. |
| **D** Stricter schema + better descriptions | ❌ | A schema checks one field at a time. "Line items must add up to the total" compares several fields, so a schema can't enforce it. The syntax is already perfect; the problem is meaning. |

## Where it comes from

**Guide D4 › 4.4 Validation, Retry & Feedback** (as recorded in Q113, Q114):
- *"The correct retry pattern sends three pieces of information back to the model."* *"The original document, so the model has the source to re-examine"*, *"The failed extraction, so the model can see what it produced"*, *"The specific validation error, so the model knows exactly what went wrong"*. (**C**: exactly these three)
- *"This works far better than a naive retry, which sends no specific error. Without the specific error, the model has no guidance and usually repeats the mistake."* (**A**)
- *"Semantic validation errors have correct structure but incorrect values: line items that do not sum, dates in the wrong order, values in wrong fields."* (**D**: schema-valid but wrong)

**Guide D4 › 4.3** (as recorded in Q35): *"tool_use with JSON schemas eliminates syntax errors but does not prevent semantic errors."* (**D**)

</details>
