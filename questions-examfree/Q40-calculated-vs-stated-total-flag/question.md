# Q40

*Source: practice set (pasted text).*

## Question

Your extraction pipeline processes invoices and extracts line items, subtotals, tax amounts, and grand totals. During evaluation, you discover that in 18% of extractions, the sum of extracted line item amounts doesn't match the extracted grand total, sometimes due to OCR errors in the source document, sometimes due to extraction mistakes by the model. Downstream accounting systems reject records with mismatched totals. What's the most effective approach to improve extraction reliability?

- **A.** Add a "calculated_total" field where the model sums extracted line items alongside a "stated_total" field. Flag records for human review when values differ.
- **B.** Extract line items and totals independently, then use a separate validation model to reconcile discrepancies by determining which extracted values are most likely correct.
- **C.** Add few-shot examples demonstrating invoices where extracted line items sum correctly to the stated total, encouraging the model to produce mathematically consistent extractions.
- **D.** Implement post-processing that automatically adjusts line item amounts proportionally when their sum doesn't match the stated total.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Make the mismatch visible (stated vs calculated total) and send it to a person; don't paper over it
**Domain / lesson:** D4 › 4.4 Validation, Retry & Feedback
**Also touches:** D5 › 5.5 Human Review
**Similar to:** [Q113](../../questions/Q113-few-shot-varied-document-structures/question.md) in the main set · [Q32](../Q32-retry-with-document-extraction-and-errors/question.md), [Q31](../Q31-retries-cant-fetch-missing-information/question.md) in this folder

## Answer: **A**

The key detail: **some mismatches are in the invoice itself** (OCR errors). No extraction method can make those numbers correct, so the only honest move is to **detect and show** them:

```json
{"line_items": [120.00, 80.00, 45.00],
 "calculated_total": 245.00,
 "stated_total": 254.00,
 "needs_review": true}
```

The two totals sit side by side, so a mismatch can't hide. A reviewer looks at the invoice and sees whether "254" is an OCR error or a line item was missed.

| Option | Verdict | Why |
|---|---|---|
| **A** `calculated_total` + `stated_total`, flag on mismatch | ✅ | Catches every mismatch, keeps both numbers, and sends the cases that need a person to a person. |
| **B** A second model decides which values are right | ❌ | Another model **guessing**. It can't fix an OCR error in the source either, and its wrong guesses look just as confident. |
| **C** Few-shot examples where everything adds up | ❌ | Teaches the model that totals *should* match, so it may "make" them match by bending a number: the error gets hidden instead of caught. |
| **D** Scale line items until they add up | ❌ | Makes up numbers. Every line item becomes wrong just to pass the check, which is worse than rejecting the record. |

**My note:** let **code** do the adding up (it's exact); the model's job is to read the numbers. The design point is the same: keep the **stated** and the **calculated** totals as separate fields.

## Where it comes from

**Guide D4 › 4.4 Validation, Retry & Feedback** (as recorded in main-set Q113, Q114):
- The guide's own example error names both values: *"Line items sum to £450 but stated_total is £500."* (**A**: the same two fields)
- *"Semantic validation errors have correct structure but incorrect values: line items that do not sum, dates in the wrong order, values in wrong fields."* (**A**: this check finds them)
- Retries don't work for *"Information genuinely absent from the source document"*. *"Flag the extraction for human review, or return null if the schema allows null."* (**A**: an OCR error is the same kind of problem: the right number isn't in the source)

**Guide D5 › 5.5** (as recorded in main-set Q18): *"Human review is a safety check for automated extraction and classification systems."* (**A**)

**From memory, not verified in this repo:** the official exam guide's 4.4 skills mention extracting a "calculated_total" alongside a "stated_total" to flag discrepancies.

</details>
