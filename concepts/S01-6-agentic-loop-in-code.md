# The agentic loop in code (Udemy B #40 lab)

**Stage 1** of the [learning path](../study-map/learning-path.md) (Udemy B #40 Practice: The Agentic Loop in Code) · **D1 › 1.1 Agentic Loops** · **D2 › 2.1 Tool Interface Design**
**Builds on:** [S01-5-conversation-lives-in-your-code](S01-5-conversation-lives-in-your-code.md) (the #12 lab: `ask()`, `messages`, `response.content`) · [Q13](../questions/Q13-agentic-loop-model-decides-next-tool/question.md) · [Q51](../questions/Q51-stop-reason-controls-loop/question.md)

## The lab setup code

```python
# --- Lab setup (provided - just run it) ---
import shopassist_lab
from shopassist_lab import check

# Everything below this line is ordinary Claude API code.
from dotenv import load_dotenv
from anthropic import Anthropic
import json

load_dotenv()
client = Anthropic()

# The model name lives in one constant, so a later update changes one line.
MODEL = "claude-sonnet-5-5"
SYSTEM = (
    "You are ShopAssist, an online store's support assistant. "
    "Returns are accepted within 30 days of delivery. "
    "Use the tools to look up orders and to start returns. "
    "Answer in one or two sentences."
)

ORDERS = {
    "ORD-12345678": {
        "item": "jacket",
        "status": "delivered",
        "delivered_days_ago": 20,
    },
}

# Two tools: look the order up, then start the return.
TOOLS = [
    {
        "name": "lookup_order",
        "description": "Look up an order by its ID. Returns the "
                       "item, the status and how many days ago "
                       "it was delivered.",
        "input_schema": {
            "type": "object",
            "properties": {"order_id": {"type": "string"}},
            "required": ["order_id"],
        },
    },
    {
        "name": "start_return",
        "description": "Start a return for a delivered order that "
                       "is inside the 30-day window. Returns the "
                       "return label number.",
        "input_schema": {
            "type": "object",
            "properties": {"order_id": {"type": "string"}},
            "required": ["order_id"],
        },
    },
]


def lookup_order(order_id):
    return ORDERS.get(order_id, {"error": "order not found"})


def start_return(order_id):
    return {"return_label": "RET-2048", "order_id": order_id}
```

Same setup as the #12 lab, plus three new things: fake order data (`ORDERS`), the tool menu (`TOOLS`) and the two Python functions behind it.

## `TOOLS`: the menu Claude reads

**`TOOLS` is plain data**, a list of two dictionaries. It's a **menu** handed to Claude:
- Claude sees each tool's **name, description and input schema**.
- Claude **never sees** the Python functions `lookup_order()` and `start_return()`. They run only in your code.
- The API turns the menu into part of the system prompt, so it's sent with every request and counts toward the context window.

```
TOOLS (menu) ──► Claude reads it ──► Claude replies: "call lookup_order with {order_id: ...}"
                                              │
          your code runs the Python function ◄┘ ──► sends the result back to Claude
```

### The three fields

| Field | What it's for | Must you include it? |
|---|---|---|
| `name` | The label Claude writes when it wants the tool. Your code uses it to pick the Python function. Letters, digits, `_` and `-`, up to 128 characters | **Yes** |
| `description` | How Claude decides **which** tool to call and **when** | Technically **no**, but always write one |
| `input_schema` | The shape of the input Claude must send | **Yes**, with at least `"type": "object"` |

- **Description:** Guide 2.1: *"Tool descriptions are the primary mechanism LLMs use for tool selection."* The API reference: *"Tool descriptions should be as detailed as possible."* The API docs: *"Aim for at least 3–4 sentences for each tool description."*
- **Inside `input_schema`**, only `"type": "object"` is mandatory (a tool's input is always a set of named fields). `"properties"` lists the fields; `"required"` makes some compulsory. A tool with no input can be just `{"type": "object"}`. In `lookup_order`, `"required": ["order_id"]` is what stops Claude calling it without an ID.

*Practical tip, not lab wording:* there's no hint about the ID's **format**. `"description": "Order ID, e.g. ORD-12345678"` inside `order_id` would help. Guide 2.1 lists *"What inputs it expects: data types, formats, constraints, and required versus optional fields"* as one part of a good description.

### Is the schema a standard?

Two layers, like a letter in an envelope:

| Layer | Whose is it |
|---|---|
| **The letter:** `type`, `properties`, `required` | **JSON Schema**, an open standard (json-schema.org), used well beyond AI. API docs: `input_schema` is *"A JSON Schema object defining the expected parameters for the tool."* |
| **The envelope:** the key `input_schema`, next to `name` and `description` | **The Claude API's** format. Other systems wrap the same JSON Schema with their own names |

The one that matters for the exam is **MCP** (D2):

| | Claude API (`TOOLS`) | MCP server tool |
|---|---|---|
| Name | `name` | `name` |
| What it does | `description` | `description` |
| Input shape | `input_schema` | `inputSchema` |
| Extras | `strict`, `input_examples` | `title`, `outputSchema`, `annotations` |

### `"strict": true`: the schema becomes a hard rule

- **Without `strict`:** Claude reads the schema and **usually** follows it. It might send `"2"` (text) where you asked for `2` (a number), or leave out a required field.
- **With `"strict": true`:** the API **guarantees** the tool input matches the schema. While Claude writes the tool call, the API only lets it produce text that still fits; the docs call this *"grammar-constrained sampling."*

**Analogy:** a paper form vs an online form. On paper you're *asked* to write a number in the "passengers" box. An online form *won't let* you type "two" there.

Strict mode supports most of JSON Schema, not all of it (API docs: *"standard JSON Schema format with some limitations"*).

> **A schema checks shape, not meaning.** Even with `strict: true`, `{"order_id": "ORD-12345678"}` is valid for an order delivered 45 days ago. Same lesson as Q114: valid JSON, wrong content.

### The 30-day rule is only text

`start_return`'s description says *"for a delivered order that is inside the 30-day window"*. That tells Claude to call `lookup_order` first: nothing forces the order, Claude works it out from the descriptions. (The comment `# Two tools: look the order up, then start the return.` is a Python comment. **Claude never sees it.**)

But the function **checks nothing**: for a 45-day-old order, or one that doesn't exist, it still hands out `RET-2048`.

- *Check: a customer writes "Please return ORD-12345678", but `ORDERS` says `"delivered_days_ago": 45`. (1) Which parts of the code try to stop the return? (2) Is any of them a guarantee? (3) What one change would make it one?*
  *My answer:* "Once the lookup_order tool is used, the order's delivered_days_ago should be read. No guarantee modes will do that. It is better to embed that in the code."
  - (1) 🟡 That's *how* the attempt works. The two places that state the rule are **`SYSTEM`** ("Returns are accepted within 30 days of delivery") and **`start_return`'s description** ("inside the 30-day window").
  - (2) ✅ Neither is a guarantee: both are text that Claude reads.
  - (3) ✅ Put the check in the code:

  ```python
  def start_return(order_id):
      order = ORDERS.get(order_id)
      if order is None:
          return {"error": "order not found"}
      if order["status"] != "delivered" or order["delivered_days_ago"] > 30:
          return {"error": "not eligible for return",
                  "reason": f"delivered {order['delivered_days_ago']} days ago; the limit is 30"}
      return {"return_label": "RET-2048", "order_id": order_id}
  ```

  **Key detail:** the function reads `delivered_days_ago` **itself**, from `ORDERS`. Don't add a `days` parameter for Claude to fill in, or Claude would be supplying the number the gate checks. The error goes back to Claude as the tool result, so Claude can explain the refusal.

> **The description asks; code in the function guarantees.**

## Task 1: map each tool name to its function

```python
TOOL_FUNCTIONS = {"lookup_order": ...,    # lookup_order - the function itself
                  "start_return": ...}    # start_return

name, args = "lookup_order", {"order_id": "ORD-12345678"}   # what a tool_use block carries
output = TOOL_FUNCTIONS[...](**args)   # name - the tool Claude asked for
print(name, "->", output)

check("tool_map", tool_functions=TOOL_FUNCTIONS, output=output)
```

*(The lab's `try`/`except` around the call is left out here. It only turns an unfilled `...` into a clear error message.)*

### The idea: a phone book from name to function

Claude's `tool_use` block gives the tool's name as **text**: `"lookup_order"`. Python can't run a word, so `TOOL_FUNCTIONS` is a **phone book**:
- **key:** the name exactly as Claude writes it, in quotes;
- **value:** the Python function that does the work.

A dictionary that maps names to functions is called a **dispatch table**. Why not `if` statements: Claude decides which tool comes next, and your code can't know in advance. One lookup works for any tool, and a third tool means one more line.

### "The function itself, no quotes, no `()`"

| You write | What it is | Analogy |
|---|---|---|
| `"lookup_order"` | **Text**, just the name | The word "recipe" on paper |
| `lookup_order()` | **Calls** it now and gives you its **result** | The finished cake |
| `lookup_order` | **The function itself**, ready to call later | The recipe card |

The phone book stores the **recipe card**. With `()`, Python would run the function when the dictionary is built, before Claude asked for anything (and here it would fail, because no `order_id` was given).

This works because Python has **first-class functions**: a function is an object, like a number or a string, so you can put it in a variable or a dictionary.

```python
f = print      # f is a second name for the same function (a "function reference")
f("hello")     # the () runs it: prints hello
```

### `TOOL_FUNCTIONS[name](**args)`: two steps

1. **`TOOL_FUNCTIONS[name]`** looks up the name and gives back the function.
2. **`(**args)`** runs it with Claude's input. `**` unpacks the dictionary into named arguments:

```python
lookup_order(**{"order_id": "ORD-12345678"})
# is the same as
lookup_order(order_id="ORD-12345678")
```

**This is the line where the tool actually runs.** In Task 1 it runs once, with pretend input. In the real loop, it runs once per `tool_use` block, with Claude's real input.

### How does `**args` "know" the schema's properties?

**It doesn't.** Python never sees the schema. They line up only because **the same name is used everywhere**:

```
1. Schema (in TOOLS)     "properties": {"order_id": ...}     ← tells Claude what to write
2. Claude's input        {"order_id": "ORD-12345678"}        ← Claude follows the schema
3. ** unpacks it         lookup_order(order_id="ORD-12345678")
4. Your function         def lookup_order(order_id):          ← must have a parameter with that name
```

Nothing checks that 1 matches 4. That's the developer's job. If the schema said `"orderId"`, the call would fail with `TypeError: lookup_order() got an unexpected keyword argument 'orderId'`.

### The other two lines

- **`name, args = "lookup_order", {...}`:** Task 1 doesn't call Claude yet, so this line **pretends to be Claude's reply**: the two parts of a `tool_use` block the dispatch needs. In the real loop they come from the block: `block.name`, `block.input`. (A comma on both sides assigns two variables at once.)
- **`check(...)`:** the **lab's grader** from `shopassist_lab`, not part of the Claude API. Its code isn't visible; judging from its arguments, it checks that each name maps to the right function and that the call returned the jacket's details.

## Task 2: a tool_result for every tool_use block

```python
def run_tools(response):
    results = []
    for block in response.content:
        if block.type == "tool_use":
            print(f"  run {block.name}({block.input})")
            output = TOOL_FUNCTIONS[...](**...)   # block.name, block.input
            results.append({"type": "tool_result",
                            "tool_use_id": ...,   # block.id
                            "content": json.dumps(output)})
    return results

question = [{"role": "user", "content":
             "I want to return the jacket from order ORD-12345678."}]
first = client.messages.create(model=MODEL, max_tokens=500,
                               output_config={"effort": "low"},
                               system=SYSTEM, tools=TOOLS, messages=question)
results = run_tools(first)
print(results)

check("every_result", run_tools=run_tools, results=results)
```

*(The lab's `try`/`except` around `run_tools(first)` is left out, as in Task 1.)*

### When does Claude send a `tool_use` block?

**In its reply to your request, whenever it decides it needs a tool to answer.**

1. Your code sends the tools menu and *"I want to return the jacket from order ORD-12345678."*
2. Claude: "I can't answer without the order details, so I need `lookup_order`."
3. Claude replies with a `tool_use` block instead of a final answer, and `stop_reason: "tool_use"`, meaning "I stopped because I need a tool."

> **Claude doesn't run anything.** It writes a request ("run this tool with this input") and stops. Your code runs the function and sends the result back in the next request. That back-and-forth is the agentic loop.

### What the blocks look like in Claude's response

Roughly what the API sends back for the jacket question (raw JSON, trimmed; the `id` is illustrative):

```json
{
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "Let me look up that order."
    },
    {
      "type": "tool_use",
      "id": "toolu_01A09q90qw90lq917835lq9",
      "name": "lookup_order",
      "input": {"order_id": "ORD-12345678"}
    }
  ],
  "stop_reason": "tool_use"
}
```

| Block | Fields |
|---|---|
| **text** | `type`, `text` |
| **tool_use** | `type`, `id`, `name`, `input`: exactly what Task 2 uses |

- **Dots vs brackets:** the Python SDK turns each block into an object, so you write `block.name`, not `block["name"]`. Same data.
- Other block types can appear, such as `thinking`. The `if block.type == "tool_use"` line skips them.
- **To see the real reply:** after the `first = client.messages.create(...)` call, add `print(first.model_dump_json(indent=2))`.

### `for block in response.content:`

`response.content` is a **list of blocks**. The loop takes them one at a time (first the text block, then the tool_use block), and `if block.type == "tool_use":` skips everything that isn't a tool request.

**Why a loop:** one reply can contain **several** `tool_use` blocks (say, two orders looked up at once), and each one needs its own result.

### `print(f"  run {block.name}({block.input})")`

A log line for **you**; it doesn't run anything. An **f-string** replaces each `{ }` with its value:

```text
  run lookup_order({'order_id': 'ORD-12345678'})
```

`{'order_id': ...}` is the input dictionary printed the Python way, with single quotes.

> **One reply can ask for several tools. Your code loops over the blocks, runs each `tool_use` by name, and answers each one by its `id`.**

## Task 3: loop until Claude stops asking for tools

```python
messages = [{"role": "user", "content":
             "I want to return the jacket from order ORD-12345678."}]

turn = 0
while True:
    turn += 1
    response = client.messages.create(
        model=MODEL,
        max_tokens=500,
        output_config={"effort": "low"},
        system=SYSTEM,
        tools=TOOLS,
        messages=messages,
    )
    print(f"turn {turn}: stop_reason = {response.stop_reason}")
    if response.stop_reason != ...:   # "tool_use" - anything else: Claude is done
        break
    messages.append({"role": "assistant", "content": ...})   # response.content
    messages.append({"role": "user", "content": run_tools(...)})   # response
    if turn == 10:   # provided: a safety net for this lab
        raise RuntimeError("Ten turns and Claude is still asking for tools. "
                           "Check what goes back into messages each turn.")

for block in response.content:
    if block.type == "text":
        print(block.text)

check("loop", response=response, messages=messages, turn=turn)
```

*(The lab's `try`/`except` around the loop is left out, as in Tasks 1 and 2.)*

### How it differs from Task 2

Both handle several tools, but in different directions:

| | Task 2: `for` loop | Task 3: `while` loop |
|---|---|---|
| Loops over | the blocks inside **one** reply | **replies**, one request after another |
| Several tools means | side by side, asked for at once | one after another, each **depending on the previous result** |
| Sends results back to Claude? | No, it only builds the list | **Yes**, and that's what keeps it going |

For the jacket, it should go roughly like this:

```
turn 1: Claude asks for lookup_order                  → "tool_use"  → run it, send the result back
turn 2: Claude sees "20 days", asks for start_return  → "tool_use"  → run it, send the result back
turn 3: Claude writes the final answer                → "end_turn"  → leave the loop
```

Claude can't ask for `start_return` in turn 1: it doesn't know the delivery date until the lookup result comes back. Task 2's `run_tools()` is reused **inside** the loop, to handle whatever each reply asked for.

### How it works: five steps per round

| Step | What happens | Code |
|---|---|---|
| 1. send | the whole conversation + tools go to Claude | `response = client.messages.create(..., tools=TOOLS, messages=messages)` |
| 2. print | show the `stop_reason` | `print(f"turn {turn}: stop_reason = ...")` |
| 3. stop? | Claude isn't asking for a tool → leave | `if response.stop_reason != "tool_use": break` |
| 4. save | Claude's reply, exactly as it came (role `assistant`) | `messages.append({"role": "assistant", "content": response.content})` |
| 5. answer | run the tools, add their results (role `user`) | `messages.append({"role": "user", "content": run_tools(response)})` |

- **`while True` + `turn`:** `while True` would go round forever, so `break` is the only normal way out. `turn` counts rounds. `if turn == 10` is a safety net in case Claude never stops asking.
- **After the loop:** `response` holds Claude's last reply, the final answer. The `for` loop prints its text blocks.
- **`messages` grows every round:** each round adds Claude's tool request and your tool results, so the next request carries everything so far. It's the #12 lab's history list, now with tool turns in it.

### `break` leaves the whole loop

`break` ends the `while` loop **completely**, skipping steps 4 and 5, and Python continues at the first line after the loop (`for block in response.content:`). The keyword that skips **just one cycle** is `continue`; this lab doesn't use it.

**Quotes matter:** compare with `"tool_use"`, the text. Without quotes, Python looks for a **variable** called `tool_use` and fails with `NameError`.

### The final reply is never saved

*My observation:* "If the response to the first message happens to be text-only, the while loop breaks and the history is not amended with that text?" ✅ Right, and it's true of **every** run, not only the first reply: the `break` comes **before** step 4, so Claude's final answer never reaches `messages`.

```
turn 1: tool_use  → saved ✓, results saved ✓
turn 2: tool_use  → saved ✓, results saved ✓
turn 3: end_turn  → break → final answer NOT saved ✗
```

- **In this lab it doesn't matter:** there's one customer question, and the final answer is still in `response`.
- **In a real chat it would:** on a follow-up, Claude wouldn't see its own last answer (the #12 lesson). Fix, one line after the loop:

  ```python
  messages.append({"role": "assistant", "content": response.content})
  ```

  *(Left out of the lab code on purpose: `check("loop", ...)` inspects `messages` and may expect the list without it.)*

> **The `while` loop runs rounds until `stop_reason` isn't `"tool_use"`. Each round: send everything, save Claude's reply, run its tools, send the results back. Claude picks the tools and their order; your loop decides when to stop.**
