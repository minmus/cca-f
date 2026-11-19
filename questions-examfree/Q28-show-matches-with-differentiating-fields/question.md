# Q28

*Source: practice set (pasted text).*

## Question

Your CRM agent's delete_contact tool handles requests like "delete the duplicate entry for Acme Corp." The database contains similarly named records (e.g., "Acme Corp," "Acme Corporation," "ACME Corp Inc."), and analytics show 8% of deletions are reversed within 24 hours due to misidentified records. Users have also complained that the current multi-step confirmation flow adds too much friction to routine cleanup tasks. Which approach most effectively reduces the error rate while maintaining workflow efficiency?

- **A.** Present matched records with differentiating fields and require single-click confirmation of the intended target before executing deletion.
- **B.** Require users to supply the exact record ID from the CRM Interface rather than using natural language references to contact names.
- **C.** Deploy automated duplicate detection that identifies and merges probable duplicates, removing the need for manual deletion requests.
- **D.** Implement soft-delete with a 30-day recovery window so users can undo mistakes without slowing down the deletion workflow.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Ambiguous match before a delete: show the candidates side by side, confirm with one click
**Domain / lesson:** D5 › 5.2 Escalation & Ambiguity Resolution (several matching records)
**Also touches:** D5 › 5.5 Human Review (a confirmation must show what to check)
**Similar to:** [Q18](../../questions/Q18-meaningful-confirmation/question.md), [Q16](../../questions/Q16-escalation-triggers/question.md) in the main set · [Q11](../Q11-preview-token-binds-execution/question.md) in this folder

## Answer: **A**

The question asks for **two** things: fewer wrong deletions **and** less friction. Only A does both:

```text
Which "Acme" should I delete?
  ( ) Acme Corp          · Berlin · 12 contacts · last activity 2 days ago
  (•) Acme Corporation   · Berlin · 0 contacts  · created yesterday (likely duplicate)
  ( ) ACME Corp Inc.     · Austin · 40 contacts · last activity today
                                                    [Delete selected]
```

- **Fewer errors:** the fields that tell the records apart (city, contacts, dates) are right there, so the user can see which one is the duplicate.
- **Less friction:** one click replaces the multi-step flow.

| Option | Errors ↓? | Friction ↓? | Why |
|---|---|---|---|
| **A** Matches + differentiating fields + one click | ✅ | ✅ | The user picks the right record with the information to do it. |
| **B** Users must type the exact record ID | ✅ | ❌ | Accurate, but makes friction **worse**: open the CRM, find the ID, copy it. It also throws away the natural-language requests the agent exists for. |
| **C** Automatic duplicate merging | ❌ | ✅ | Makes the **same** "which Acme is which?" guess, now with no human checking, and merging is harder to undo than deleting. |
| **D** Soft-delete, 30-day undo | ❌ | ✅ | Makes mistakes recoverable but doesn't make them less frequent: 8% are still wrong. A good **addition**, not the fix. |

## Where it comes from

**Main-set [Q18](../../questions/Q18-meaningful-confirmation/question.md)** (guide D5 › 5.5 and D1 › 1.4, as recorded there):
- *"Human review is a safety check for automated extraction and classification systems."* A check only works if the reviewer can **see** what to check, so show the fields that tell the records apart. (**A**)
- A handoff summary *"is the only information they receive, so it must be self-contained."* The same applies to a confirmation prompt. (**A**)

**Main-set [Q16](../../questions/Q16-escalation-triggers/question.md)** (guide D5 › 5.2, from the exam-trap notes there, paraphrased, not a quote): when a customer match is ambiguous, don't guess (e.g. pick the most recent record); **ask** with more identifying details. (**A**, **C**)

No verbatim guide quote on deleting from ambiguous matches is recorded in this repo.

</details>
