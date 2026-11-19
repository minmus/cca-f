# Q39

*Source: practice set (pasted text).*

## Question

Your extraction system uses tool_use with a JSON schema containing 12 fields and detailed descriptions, totaling approximately 2,500 tokens for the complete tool definition. Processing documents under 150K tokens yields 98% accuracy. For documents between 175-190K tokens, accuracy drops to 71%, with information from the final third consistently missed. The model's context window is 200K tokens. What is the most likely cause?

- **A.** Tool definitions consume input context tokens. Combined with system prompts and document content, the total approaches the context limit, degrading end-of-document processing.
- **B.** Very long documents exceed the model's effective attention span regardless of context limits, causing accuracy degradation for content farther from the prompt instructions.
- **C.** The model distributes attention proportionally across input length, causing fields mentioned only once near the document's end to receive insufficient processing focus.
- **D.** Schemas exceeding 8-10 fields increase decision complexity during parameter generation, reducing extraction accuracy independent of document length.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Everything shares one context window: tool definitions, system prompt, document and output
**Domain / lesson:** D5 › 5.1 Context Window Management
**Also touches:** D4 › 4.3 Structured Output with Tool Use (the tool definition is part of the input)

## Answer: **A**

Add up the budget for a 190K-token document:

```text
  document                         190,000
+ tool definition (12 fields)        2,500
+ system prompt + tool-use overhead  a few thousand
+ room for Claude's answer           a few thousand
= at or over 200,000  ← the window is full
```

Under 150K there's ~50K of room, hence 98%. Near 190K there's none, and what suffers is the **end** of the document, the part that no longer fits.

| Option | Verdict | Why |
|---|---|---|
| **A** Tool definitions + prompt + document fill the window | ✅ | The numbers add up to the limit, and the failure appears exactly when they do. |
| **B** "Effective attention span", regardless of limits | ❌ | Ignores the numbers in the question. The guide says models handle the **beginning and end** of long inputs reliably and miss the **middle**, but here it's the end that's lost. |
| **C** Attention spread "proportionally" across the input | ❌ | Not a real mechanism, and it wouldn't explain the sudden drop at one size. |
| **D** Too many fields (over 8–10) | ❌ | The same 12-field schema gets 98% on shorter documents, so the field count isn't the problem. |

**Fix:** split very long documents into chunks with room to spare, or trim the tool descriptions.

**My note:** the API doesn't silently drop part of your input. If the input alone exceeds the window, it returns an error. If you see the end "missed" with no error, check whether your own code cuts the document to fit, or whether Claude ran out of room for its answer.

## Where it comes from

**Guide D5 › 5.1 Context Window Management** (as recorded in main-set [Q38](../../questions/Q38-sliding-window-recent-turns/question.md) and concept note [S01-2](../../concepts/S01-2-context-window-across-sessions.md)):
- *"The context window is everything Claude reads on one request."* (**A**: tool definitions included)
- *"Models process information at the beginning and end of long inputs reliably."* *"A model may miss findings in the middle of a long context, or may give them less weight."* (**B**, **C**: the end isn't where attention fails)

**Claude API docs, Context windows** (as recorded in main-set [Q110](../../questions/Q110-resubmit-only-failures-chunked/question.md)):
- *"If the input alone already exceeds the model's context window, the API returns a 400 invalid_request_error ("prompt is too long") on every model."* (my note)
- *"On Claude 4.5 models and newer, if input tokens plus `max_tokens` exceeds the context window size, the API accepts the request."* (input and output share the window)

</details>
