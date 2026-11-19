# Q06

## Question

You are building a classifier to categorize customer reviews, but the model struggles with ambiguous scenarios like sarcasm and mixed sentiments. You decide to use few-shot prompting. What is the recommended best practice for this scenario?

- **A.** Provide exactly 1 example of a standard positive review to establish the JSON format.
- **B.** Provide 8 to 10 examples to ensure every possible sentiment category is covered comprehensively.
- **C.** Provide 2 to 4 examples, ensuring that at least one example specifically addresses an ambiguous edge case like sarcasm.
- **D.** Avoid few-shot prompting and instead use a detailed system prompt explaining the exact definition of sarcasm.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Few-shot: 2 to 4 examples that target the ambiguous cases
**Domain / lesson:** D4 › 4.2 Few-Shot

## Answer: **C**

| Option | Verdict | Why |
|---|---|---|
| **A** Exactly 1 standard positive example | ❌ | One example is too few to set a pattern, and an easy positive review teaches nothing about sarcasm, which is where the model fails. |
| **B** 8 to 10 examples covering every category | ❌ | More than 4 examples costs tokens without matching benefit. The guide lists "many examples to cover every case" as a trap: pick examples for the cases that fail, don't try to cover everything. |
| **C** 2 to 4 examples, at least one on sarcasm | ✅ | The right number, aimed at the cases that cause the problem. Ideally each example also shows its reasoning ("praise words, but the meaning is negative → negative"), so the model can apply the same judgment to new cases. |
| **D** No examples, a detailed definition instead | ❌ | Ambiguous judgments like sarcasm are learned better from examples than from definitions. The guide says few-shot beats more instructions for consistent output. |

## Where it comes from

**Guide D4 › 4.2 Few-Shot** (see [Q112](../../questions/Q112-few-shot-standard-materials-format/question.md), [Q121](../../questions/Q121-few-shot-acceptable-vs-genuine-findings/question.md)):
- *"Use 2 to 4 targeted examples. Fewer than 2 examples do not establish a pattern. More than 4 examples waste tokens without proportional benefit."* (**A**, **B**, **C**)
- *"Choose examples that cover the specific ambiguous cases that cause problems."* (**C**)
- *"These ambiguous cases need examples that show the correct judgement, with reasoning."* (**C**)
- *"Each example must show reasoning."* *"The reasoning teaches the model to generalise its judgement to novel patterns, instead of only matching the cases in your examples."* (**C**)
- Trap: *"Adding many examples to cover every case and variant."* (**B**)
- *"Few-shot examples are the most effective technique for consistent, well-formatted output from Claude. More instructions, confidence thresholds and temperature adjustments all do less."* (**D**)

**Claude docs (prompting best practices):** *"Provide examples of the output you want. This is more effective than abstract instructions."* (**D**)

</details>
