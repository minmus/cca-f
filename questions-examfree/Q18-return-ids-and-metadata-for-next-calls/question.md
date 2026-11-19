# Q18

*Source: practice set (pasted text).*

## Question

Your documents (query) tool returns results as "Found 3 documents: Q2 Budget Proposal, Q2 Budget Forecast, Annual Review". You want the agent to document (4, multi) and doc (24, multi). What return format would best enable these multi-step workflows?

- **A.** URLs that users can click to open the document in their browser.
- **B.** Structured data containing document IDs and metadata for each result.
- **C.** A JSON array of document titles extracted from the search results.
- **D.** More detailed human-readable descriptions including the size and authors.

*The middle of the question is garbled in the source. It most likely said: you want the agent to pass the results to other tools, such as `get_document(doc_id)` or `summarize_document(doc_id)`, in multi-step workflows. (My reading.)*

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Search results that feed the next tool call: return IDs and metadata, not prose
**Domain / lesson:** D2 › 2.1 Tool Interface Design (output contracts)
**Similar to:** [Q35](../../questions/Q35-structured-tool-output-fields/question.md) in the main set

## Answer: **B**

The next tools need a **document ID**, and "Found 3 documents: Q2 Budget Proposal, …" has none. Structured results give the agent both what it needs:

```json
[{"id": "doc_8812", "title": "Q2 Budget Proposal", "type": "proposal", "updated": "2026-06-30"},
 {"id": "doc_8813", "title": "Q2 Budget Forecast", "type": "forecast", "updated": "2026-07-02"}]
```

- the **ID** to pass to the next tool: `get_document("doc_8812")`;
- the **metadata** (title, type, date) to choose the right document.

| Option | Verdict | Why |
|---|---|---|
| **A** Clickable URLs | ❌ | Made for a **person** in a browser, not for the agent's next tool call. |
| **B** IDs + metadata per result | ✅ | IDs make the next call possible; metadata lets the agent pick the right document. |
| **C** JSON array of titles | ❌ | Structured, but still no IDs. The agent would have to look each one up again by title, and titles can clash ("Q2 Budget…"). |
| **D** Longer human-readable descriptions | ❌ | Still prose with no IDs, and more for the agent to parse. |

## Where it comes from

**Anthropic engineering blog, "Writing effective tools for AI agents"** (read 2026-11-19):
- *"In some instances, agents may require the flexibility to interact with both natural language and technical identifiers outputs, if only to trigger downstream tool calls (for example, `search_user(name='jane')` → `send_message(id=12345)`)."* (**B**: the ID for the next call, the title for understanding)
- In its Slack example, IDs *"can be retrieved from a "detailed" tool response to enable further tool calls that require these."* (**B**)
- *"Agents also tend to grapple with natural language names, terms, or identifiers significantly more successfully than they do with cryptic identifiers."* (**B**: keep the titles alongside the IDs)

**Guide D2 › 2.1 Tool Interface Design** (as recorded in Q35): tools should be purpose-specific *"with defined input and output contracts."* (**B**)

</details>
