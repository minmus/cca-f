# Q30

*Source: practice set (pasted text). The right edge of the paste was cut off; missing words are filled in [in brackets] as my best reading.*

## Question

The system processes product reviews using tool use with a defined schema: rating (integer 1-5), pros (string array), cons (string array), and overall_sentiment (enum: positive, ne[gative, neutral]).

Testing reveals two issues with brief or ambiguous reviews (~20% of the dataset): (1) for reviews like "Great product!", Claude fabricates specific pros and cons rather than indica[ting the] information isn't explicitly stated, and (2) for sarcastic reviews like "Well that was... interesting", Claude picks sentiment arbitrarily since there's no option for ambiguous cases. W[hich schema] modification best addresses both issues?

- **A.** Make pros and cons optional fields, and add "neutral" and "unclear" to the sentiment enum.
- **B.** Allow empty arrays for pros/cons as valid output, and add "unclear" to the sentiment enum.
- **C.** Add an extraction_confidence field (0.0-1.0) for each value, and filter outputs where any confidence falls below a threshold.
- **D.** Allow null values for pros/cons, and add "unclear" to the sentiment enum.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Give the schema an honest way to say "not stated" (null) and "can't tell" ("unclear")
**Domain / lesson:** D4 › 4.3 Structured Output with Tool Use
**Similar to:** [Q112](../../questions/Q112-few-shot-standard-materials-format/question.md), [Q113](../../questions/Q113-few-shot-varied-document-structures/question.md) in the main set · [Q29](../Q29-schema-guarantees-structure-not-meaning/question.md) in this folder

## Answer: **D**

Both problems have the same cause: **the schema has no honest answer**, so Claude makes one up.

| Problem | What the schema forces | Fix in D |
|---|---|---|
| "Great product!": no specific pros/cons stated | Fill the arrays with *something* → invented pros | `pros: null` = "not stated in the review" |
| "Well that was... interesting": sarcasm | Pick positive/negative/neutral → a guess | `overall_sentiment: "unclear"` |

| Option | Verdict | Why |
|---|---|---|
| **A** Optional fields + "neutral" + "unclear" | ❌ | A missing field is a weaker signal than an explicit `null`: did Claude decide "not stated", or just skip it? And "neutral" (the enum most likely has it already) means "neither good nor bad", which is not the same as "can't tell". |
| **B** Empty arrays + "unclear" | ❌ | Close, but `[]` reads as "the reviewer listed **no** pros", a fact. `null` says "the review **doesn't say**". Only null matches issue 1's wording: *"information isn't explicitly stated"*. (My reasoning, not a guide quote.) |
| **C** Confidence per value + filter | ❌ | Doesn't give Claude a way to say "not stated" or "unclear", so it still invents; it just drops some rows afterwards. Raw model confidence isn't calibrated either. |
| **D** Nullable pros/cons + "unclear" | ✅ | Each problem gets an honest answer in the schema itself. |

## Where it comes from

**Guide D4 › 4.3 Structured Output with Tool Use** (as recorded in Q112, Q113):
- *"A required field pressures the model to produce a value even when the source has none. A nullable field lets the model honestly return null."* (**D**)
- *"When a source document may lack some information, make the fields for that information optional or nullable."* (**A**, **D**)
- *"If a value is genuinely absent, no number of retries will produce a correct value. Flag the extraction for human review, or return null if the schema allows null."* (**D**)

**Guide D5 › 5.5** (as recorded in Q114): *"The raw confidence scores from the model are not calibrated."* (**C**)

**The "unclear" enum value:** no guide quote on this is recorded in the repo. From memory, the official exam guide's 4.3 skills mention adding values like "unclear" for ambiguous cases (and "other" plus a detail field). Not verified here.

</details>
