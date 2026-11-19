# Q34

*Source: practice set (pasted text).*

## Question

Your extraction pipeline processes contracts that frequently include amendments. When a contract contains both original terms and later amendments (e.g., original clause specifies "30-day payment terms" while Amendment 1 changes this to "45 days"), the model inconsistently extracts one value or the other with no indication of which applies. What's the most effective approach to improve extraction accuracy for documents with amendments?

- **A.** Preprocess documents with a classifier that identifies and removes superseded sections before the main extraction step.
- **B.** Implement post-extraction validation using pattern matching to detect amendments and flag those extractions for manual review.
- **C.** Redesign the schema so amended fields capture multiple values, each with source location and effective date.
- **D.** Add prompt instructions to always extract the most recent amendment value and ignore superseded original terms.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Amended terms: keep every value with where it came from and when it applies
**Domain / lesson:** D4 › 4.3 Structured Output with Tool Use (schema design)
**Also touches:** D5 › 5.6 Information Provenance (sources and dates)
**Similar to:** [Q82](../../questions/Q82-dates-turn-conflicts-into-trends/question.md) in the main set · [Q12](../Q12-annotate-conflict-both-values-attributed/question.md), [Q30](../Q30-nullable-fields-and-unclear-enum/question.md) in this folder

## Answer: **C**

The schema has room for **one** value, but the document has **two**. So the model has to pick, and it picks inconsistently. C gives it room for both:

```json
"payment_terms": [
  {"value": "30 days", "source": "Section 4.2 (original)", "effective_date": "2024-01-01"},
  {"value": "45 days", "source": "Amendment 1, §2",        "effective_date": "2025-03-01"}
]
```

Now nothing is lost, the output is the same every time, and code (or a person) can work out which term applies on any date.

| Option | Verdict | Why |
|---|---|---|
| **A** A classifier removes superseded sections first | ❌ | Another model step that can be wrong, and it **deletes** evidence before anyone can check it. An amendment may change only part of a clause, or start in the future. |
| **B** Pattern-match amendments, send to manual review | ❌ | Doesn't improve the extraction at all; it just sends more work to people. |
| **C** Multiple values, each with source and effective date | ✅ | Matches the real document: several values, each tied to where and when. Consistent, and nothing is thrown away. |
| **D** Prompt: always take the most recent amendment | ❌ | Tempting, but "most recent" isn't always "in force": an amendment can take effect later, change only some terms, or be amended again. The model still has to judge, and the original term and its history are gone. |

> **When a document holds several versions of a fact, don't make the model pick one. Capture them all with source and date, and decide afterwards.**

## Where it comes from

**Guide D5 › 5.6 Information Provenance** (as recorded in Q82, Q85):
- *"Different publication dates explain different numbers. The publication dates are temporal context, and they have to be preserved."* (**C**)
- Exam trap: *"Assuming different numbers from different sources are contradictions."* *"Require dates in structured outputs to enable correct temporal interpretation."* (**C**)
- Exam trap: *"Selecting the most recent source when two credible sources conflict."* *"Arbitrarily selecting one value destroys information."* (**D**, **A**)

**Guide D4 › 4.3** (as recorded in Q35): structured output is *"a reply whose fields and types match a fixed shape"*. If the shape allows only one value, the model has to choose. (**C** changes the shape)

</details>
