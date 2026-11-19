# Q29

## Question

When implementing structured data extraction for a application, what does utilizing tool_use with a strict JSON schema explicitly guarantee?

- **A.** Both structural compliance and semantic correctness of the extracted data.
- **B.** Structural compliance, ensuring valid JSON syntax and required fields, but not semantic accuracy.
- **C.** Elimination of all hallucinations and inaccurate data extraction.
- **D.** Automatic retry loops if the extracted values fail business logic validation.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** A strict schema guarantees the shape of the data, not that the values are right
**Domain / lesson:** D4 › 4.3 Structured Output with Tool Use
**Also touches:** D4 › 4.4 Validation, Retry & Feedback (D)
**Similar to:** [Q35](../../questions/Q35-structured-tool-output-fields/question.md), [Q113](../../questions/Q113-few-shot-varied-document-structures/question.md), [Q114](../../questions/Q114-calibrated-field-confidence-routes-review/question.md) in the main set

## Answer: **B**

The schema checks the **form**: valid JSON, required fields present, right types. It can't check the **meaning**. `"total": 120` passes the schema even when the invoice says 210.

| Option | Verdict | Why |
|---|---|---|
| **A** Structure **and** meaning | ❌ | Only structure. A wrong value in the right field still passes. |
| **B** Structure, not meaning | ✅ | Exactly what a schema does. |
| **C** No hallucinations at all | ❌ | A schema can't tell whether a value came from the document or was made up. It only checks that the field has the right type. |
| **D** Automatic retries on business-rule failures | ❌ | The schema doesn't retry anything. Checking business rules (e.g. "line items must add up to the total") and retrying with the error is **your own code**, a separate step. |

## Where it comes from

**Guide D4 › 4.3 Structured Output with Tool Use** (as recorded in Q35):
- *"tool_use with JSON schemas eliminates syntax errors but does not prevent semantic errors."* The schema *"guarantees structure, not correctness"*. (**A**, **B**)
- Exam trap: *"Believing tool_use with JSON schemas prevents all extraction errors."* (**A**, **C**)

**Guide D4 › 4.4 Validation, Retry & Feedback** (as recorded in Q113, Q114):
- *"Semantic validation errors have correct structure but incorrect values: line items that do not sum, dates in the wrong order, values in wrong fields."* (**B**)
- *"The correct retry pattern sends three pieces of information back to the model"*: the original document, the failed extraction, and *"The specific validation error"*. (**D**: a retry loop you build, not something the schema does)

**Guide D4 › 4.2 Few-Shot** (as recorded in Q113):
- *"Few-shot examples also reduce hallucination in extraction tasks."* (**C**: hallucination needs other fixes)

</details>
