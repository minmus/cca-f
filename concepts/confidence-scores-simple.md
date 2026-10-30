# Confidence scores and review decisions, in simple words

**From:** [Q21](../questions/Q21-review-flag-from-calibrated-thresholds/question.md) · **Domain / lesson:** D5 › 5.5 Human Review & Confidence Calibration

## The setup

A company receives lots of **invoices** (bills from suppliers). A program reads each invoice and pulls out three things:
- **Vendor:** who sent the bill
- **Amount:** how much to pay
- **Date:** when it's due

The program isn't perfect, so for each thing it reads it also says **how sure it is** (0 to 1):

```
vendor: "Acme Ltd"   sure: 0.97   (very sure)
amount: "$1,450"     sure: 0.62   (not very sure, maybe a blurry scan)
date:   "2026-11-03" sure: 0.91   (fairly sure)
```

## What the agent has to decide

For each invoice: **"looks fine, process it"** or **"not sure, ask a human to check"**.

## What goes wrong

The agent gets it wrong **in both directions**:
1. It sees a low "sure" number and **carries on anyway**, and the value was wrong. Wrong invoices get paid.
2. It **asks a human** when everything was right. People waste time checking good invoices.

**Why:** the agent is **guessing what each number means**. Is 0.62 bad? Is 0.75? It decides differently each time.

## The fix (Q21 answer B)

**Don't make the agent guess.** The tool decides with a fixed rule (e.g. "amount below 0.85 → a human must check") and hands the agent a clear answer:

```
request_review: true
review_reasons: ["amount is only 0.62 sure"]
```

The agent sees `request_review: true` → sends it to a human, who knows to **look at the amount**.

> **Traffic light:** instead of asking the driver to judge "how risky is this crossing?", give them a red or green light.

## My answer to the check question

**Why is a fixed rule in the tool better than letting the agent look at 0.62 and decide?**
*My answer:* "Because of the consistency: a fixed threshold won't allow low-probability situations to pass, and vice versa." ✅ **Correct.** Both failure directions are covered:

| | Agent guesses | Fixed threshold in the tool |
|---|---|---|
| Low score (0.62) | Sometimes lets it pass ❌ | **Always** → review |
| High score (0.97) | Sometimes sends it to review anyway ❌ | **Always** → proceed |
| Same input twice | May decide differently | **Same decision every time** |

Same rule as Q16: a threshold on a number is **plain data**, so it goes in **code**.

**One limit to remember:** a fixed rule is only as good as the **number you choose**. Consistent with a bad threshold = consistently wrong. That's why B says *"tested"* thresholds (see the Q21 check question).
