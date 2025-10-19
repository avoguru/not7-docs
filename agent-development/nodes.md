# Nodes Reference

Nodes are the building blocks of agents. Each node does ONE thing.

## Node Structure

```json
{
  "id": "unique-node-id",
  "name": "Human Readable Name",
  "type": "llm",
  "prompt": "Instructions for the LLM",
  "input_format": "text",
  "output_format": "json",
  "llm": {
    "model": "gpt-4",
    "temperature": 0.8
  }
}
```

## Required Fields

**`id`** - Unique identifier within the agent  
**`type`** - Node type (currently: `llm`)  

## Node Types

### LLM Node

Processes input using a Language Model.

### ReAct Node

Iterative reasoning with self-critique and refinement. The agent thinks through problems in multiple steps until reaching a satisfactory answer.

**Required fields:**
- `id` - Unique ID
- `type` - Must be `"react"`
- `react_goal` - What to research/solve

**Optional fields:**
- `name` - Human-readable name
- `max_iterations` - Maximum thinking iterations (default: 5)
- `thinking_prompt` - Custom thinking guidance
- `output_format` - Output format (default: text)
- `llm` - Override global LLM config

**Example:**
```json
{
  "id": "deep_thinker",
  "name": "Problem Solver",
  "type": "react",
  "react_goal": "Solve the river crossing puzzle: farmer with fox, chicken, and grain",
  "max_iterations": 4,
  "output_format": "text"
}
```

**How it works:**
1. Iteration 1: Initial thinking
2. Iteration 2: Self-critique and refinement
3. Iteration 3-N: Continue refining
4. Stops when satisfied (uses "FINAL:" marker) or reaches max_iterations

**Tracing:**
Every execution includes a `react_trace` showing:
- Number of iterations
- Each thinking step
- Time and cost per iteration

**ReAct with Tools:**

Enable tool calling in ReAct nodes by setting `tools_enabled: true` and providing a tool provider:

```json
{
  "id": "search_emails",
  "type": "react",
  "tools_enabled": true,
  "config": {
    "tools": {
      "provider": "arcade-gmail"
    }
  },
  "react_goal": "Search for unread emails from yesterday",
  "max_iterations": 5
}
```

The agent will autonomously call tools during reasoning to accomplish its goal

### LLM Node

Simple single-pass LLM processing.

**Required fields:**
- `id` - Unique ID
- `type` - Must be `"llm"`
- `prompt` - Instructions for the LLM

**Optional fields:**
- `name` - Human-readable name
- `input_format` - `"text"`, `"json"`, `"xml"`, `"auto"` (default: auto)
- `output_format` - `"text"`, `"json"`, `"xml"` (default: text)
- `llm` - Override global LLM config

**Example:**
```json
{
  "id": "analyze",
  "name": "Analyze Sentiment",
  "type": "llm",
  "prompt": "Analyze the sentiment of this text. Return JSON: {\"sentiment\": \"positive|negative|neutral\", \"confidence\": 0.0-1.0}",
  "input_format": "text",
  "output_format": "json"
}
```

### Tool Node

Execute external tools directly. Supports Arcade.dev toolkits (Gmail, Google Maps, Calendar, etc.)

**Required fields:**
- `id` - Unique ID
- `type` - Must be `"tool"`
- `tool_name` - Name of the tool to execute
- `tool_arguments` - Arguments for the tool
- `config.tools.provider` - Tool provider (e.g., `"arcade-gmail"`)

**Example (Send Email):**
```json
{
  "id": "send_email",
  "type": "tool",
  "config": {
    "tools": {
      "provider": "arcade-gmail"
    }
  },
  "tool_name": "SendEmail",
  "tool_arguments": {
    "recipient": "user@example.com",
    "subject": "Test Email",
    "body": "{{input}}"
  }
}
```

**Argument Placeholders:**
Use `"{{input}}"` to pass the output from the previous node as an argument.

### Transform Node (Roadmap)

Transform data between formats.

**Example (future):**
```json
{
  "id": "extract",
  "type": "transform",
  "transform": "$.users[*].email"
}
```

## Node Configuration

### The `prompt` Field

The prompt is your instruction to the LLM. Be specific about:

**Input format:**
```
"You will receive customer feedback as text..."
```

**Output format:**
```
"Return JSON: {\"theme\": \"...\", \"sentiment\": \"...\"}"
```

**Examples:**
```
"For example: {\"theme\": \"pricing\", \"sentiment\": \"negative\"}"
```

**Specific prompt:**
```json
"prompt": "You will receive customer feedback as text. Extract 3-5 key themes. Return as JSON array of strings. Example: [\"pricing\", \"performance\", \"support\"]. Be concise."
```

**Vague prompt:**
```json
"prompt": "analyze this"
```

Specific prompts with clear output formats produce better results.

### Input/Output Formats

**`input_format`:**
- `"text"` - Plain text
- `"json"` - JSON object/array
- `"xml"` - XML document
- `"auto"` - Auto-detect (default)

**`output_format`:**
- `"text"` - Plain text
- `"json"` - JSON (LLM must format correctly)
- `"xml"` - XML

**Format flow example:**
```
Node1: output_format="json" → Node2: input_format="json"
```

### LLM Overrides

Override global LLM config per node:

```json
{
  "id": "creative_writer",
  "type": "llm",
  "llm": {
    "model": "gpt-4",
    "temperature": 0.9,
    "max_tokens": 4000
  },
  "prompt": "Write creatively..."
}
```

### Per-Node Tool Configuration

Each node can specify its own tool provider, enabling multi-toolkit agents:

```json
{
  "nodes": [
    {
      "id": "find_restaurants",
      "type": "react",
      "tools_enabled": true,
      "config": {
        "tools": {
          "provider": "arcade-googlemaps"
        }
      },
      "react_goal": "Find 3 restaurants"
    },
    {
      "id": "send_email",
      "type": "tool",
      "config": {
        "tools": {
          "provider": "arcade-gmail"
        }
      },
      "tool_name": "SendEmail",
      "tool_arguments": {
        "recipient": "user@example.com",
        "subject": "Restaurant Recommendations",
        "body": "{{input}}"
      }
    }
  ]
}
```

**Benefits:**
- Use different toolkits in the same agent (Maps + Gmail + Calendar)
- Node-level config overrides agent-level config
- Tool managers are pooled and reused for efficiency

## Multiple Nodes

Agents can have multiple nodes executing in sequence:

```json
{
  "nodes": [
    {"id": "step1", "type": "llm", "prompt": "Extract themes..."},
    {"id": "step2", "type": "llm", "prompt": "Analyze sentiment..."},
    {"id": "step3", "type": "llm", "prompt": "Generate summary..."}
  ],
  "routes": [
    {"from": "start", "to": "step1"},
    {"from": "step1", "to": "step2"},
    {"from": "step2", "to": "step3"},
    {"from": "step3", "to": "end"}
  ]
}
```

**Data flow:**
```
Input → step1 → output1
         ↓
      output1 → step2 → output2
                 ↓
              output2 → step3 → final output
```

## Examples

### Single Node (Simple)

```json
{
  "id": "gen",
  "type": "llm",
  "prompt": "Write a haiku about agents",
  "output_format": "text"
}
```

### Multi-Node (Pipeline)

See [examples/feedback-analyzer.json](../../not7-core/examples/feedback-analyzer.json) for a complete 3-node pipeline.

---

[← JSON Spec](json-spec.md) | [Routes →](routes.md)

