# Q31

*Source: practice set (pasted text).*

## Question

Your extraction system implements automatic retries when validation fails. On each retry, the specific validation error is appended to the prompt. This retry-with-error-feedback approach resolves most failures within 2-3 attempts. For which failure pattern would additional retries be LEAST effective?

- **A.** The model extracts "et al." for co-authors when the full list exists only in an external document not in the input.
- **B.** The model extracts citation counts as locale-formatted strings ("1,234") when the schema requires integers.
- **C.** The model extracts dates as ISO 8601 datetime strings ("2003-03-15T00:00:00Z") when the schema requires only the date portion.
- **D.** The model extracts keywords as a nested object organized by category when the schema requires a flat array of strings.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Retries fix how a value is written, not a value that isn't in the input
**Domain / lesson:** D4 › 4.4 Validation, Retry & Feedback
**Similar to:** [Q113](../../questions/Q113-few-shot-varied-document-structures/question.md) in the main set · [Q29](../Q29-schema-guarantees-structure-not-meaning/question.md), [Q30](../Q30-nullable-fields-and-unclear-enum/question.md) in this folder

## Answer: **A**

Ask one question per option: **does the right answer exist in what the model was given?**

| Option | Is the right value in the input? | Can a retry fix it? |
|---|---|---|
| **A** Co-authors are in an **external** document | ❌ No | ❌ Never. The model can't extract names it was never shown. Retrying just repeats "et al." or makes names up. |
| **B** "1,234" instead of `1234` | ✅ Yes, wrong format | ✅ "Must be an integer" → `1234` |
| **C** Full datetime instead of a date | ✅ Yes, wrong format | ✅ "Date only" → `2003-03-15` |
| **D** Nested object instead of a flat list | ✅ Yes, wrong shape | ✅ "Flat array of strings" → flattened |

B, C and D are **format** mistakes: the data is there, and the error message says exactly how to fix it. A is **missing information**, and no error message can supply it.

**What to do for A instead:** give the model the external document, or return `null` / flag it for human review.

## Where it comes from

**Guide D4 › 4.4 Validation, Retry & Feedback** (as recorded in Q113):
- Retries don't work for *"Information genuinely absent from the source document"*, *"Data that exists only in an external document not provided to the model"*, *"Fields requiring knowledge the model does not have"*. (**A**: almost word for word)
- *"If a value is genuinely absent, no number of retries will produce a correct value. Flag the extraction for human review, or return null if the schema allows null."* (**A**)
- *"The correct retry pattern sends three pieces of information back to the model"*: the original document, the failed extraction, and *"The specific validation error, so the model knows exactly what went wrong"*. (**B**, **C**, **D**: exactly what fixes a format error)

</details>
