# Q35

*Source: practice set (pasted text).*

## Question

Your system must extract event details from calendar invitations and output JSON that strictly conforms to a schema with fields for title, date, time, location, and attendees. Downstream [systems] reject any malformed or non-conformant JSON. What approach provides the most reliable schema compliance?

- **A.** Define a tool with your target schema as input parameters and have Claude call it with the extracted data.
- **B.** Pre-fill Claude's response with an opening brace to force JSON output, then complete and parse the response.
- **C.** Append instructions like "Output only valid JSON matching the schema exactly" and implement retry logic to re-prompt when JSON parsing fails.
- **D.** Include detailed JSON formatting instructions and the target schema in your prompt, then parse Claude's text response as JSON.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Reliable JSON: make the schema a tool's input, don't ask for JSON in text
**Domain / lesson:** D4 › 4.3 Structured Output with Tool Use
**Similar to:** [Q35](../../questions/Q35-structured-tool-output-fields/question.md) in the main set · [Q29](../Q29-schema-guarantees-structure-not-meaning/question.md) in this folder

## Answer: **A**

With a tool, the schema is part of the API call, not just text in the prompt:

```python
tools = [{
    "name": "save_event",
    "input_schema": {"type": "object",
        "properties": {"title": {"type": "string"}, "date": {"type": "string"},
                       "time": {"type": "string"}, "location": {"type": "string"},
                       "attendees": {"type": "array", "items": {"type": "string"}}},
        "required": ["title", "date", "time", "location", "attendees"]}
}]
```

Claude "calls" `save_event`, and the arguments arrive as JSON in that shape. You read them from the `tool_use` block; there's no text to parse.

| Option | Verdict | Why |
|---|---|---|
| **A** Schema as a tool's input | ✅ | The structure is enforced through the tool definition, so syntax errors go away. |
| **B** Pre-fill `{` | ❌ | Makes the reply *start* like JSON, nothing more: fields can still be missing, misnamed or wrongly typed. |
| **C** "Output only valid JSON" + retry on parse errors | ❌ | Still a text request, with retries to clean up after failures. Retries cost time and money and can still fail. |
| **D** Schema and formatting rules in the prompt | ❌ | The model is *asked* to follow the schema, but nothing enforces it. Prompt-based JSON has no guarantee. |

**Reminder from Q29:** the tool schema guarantees the **shape**, not that the values are right. A wrong date in the right format still passes.

## Where it comes from

**Guide D4 › 4.3 Structured Output with Tool Use** (as recorded in main-set Q35):
- *"Structured output is a reply whose fields and types match a fixed shape."*
- *"Prompt-based JSON is plain text with no structural guarantees."* (**B**, **C**, **D**)
- *"tool_use with JSON schemas eliminates syntax errors but does not prevent semantic errors."* (**A**, and its limit)

</details>
