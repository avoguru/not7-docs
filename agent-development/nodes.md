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

### LLM Node (Currently Implemented)

Processes input using a Language Model.

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

### Tool Node (Roadmap)

Execute external tools via MCP protocol.

**Example (future):**
```json
{
  "id": "fetch_data",
  "type": "tool",
  "tool": {
    "server": "database",
    "name": "query",
    "params": {"sql": "SELECT * FROM users"}
  }
}
```

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

**Good prompt:**
```json
"prompt": "You will receive customer feedback as text. Extract 3-5 key themes. Return as JSON array of strings. Example: [\"pricing\", \"performance\", \"support\"]. Be concise."
```

**Bad prompt:**
```json
"prompt": "analyze this"
```

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

