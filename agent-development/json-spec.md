# JSON Specification Reference

Complete reference for NOT7 agent specifications.

## Top-Level Fields

```json
{
  "id": "my-agent",
  "version": "1.0.0",
  "goal": "Agent description",
  "config": { ... },
  "nodes": [ ... ],
  "routes": [ ... ]
}
```

### `id` (optional)

Unique identifier for the agent.

- **Required** when deploying via `POST /api/v1/agents`
- **Optional** for anonymous execution
- Used for triggering: `POST /api/v1/agents/{id}/run`

**Example:**
```json
"id": "daily-report-agent"
```

### `version` (required)

Semantic version of the agent spec.

**Example:**
```json
"version": "1.0.0"
```

### `goal` (required)

Human-readable description of what the agent does.

**Example:**
```json
"goal": "Analyze customer feedback and generate insights"
```

### `config` (optional)

Global configuration for the agent.

```json
"config": {
  "llm": {
    "provider": "openai",
    "model": "gpt-4",
    "temperature": 0.7,
    "max_tokens": 2000
  },
  "constraints": {
    "max_time": "5m",
    "max_cost": 1.0
  }
}
```

**Fields:**
- `llm` - Default LLM settings (can be overridden per node)
- `constraints` - Execution limits (not yet enforced in v0.1)

### `nodes` (required)

Array of execution steps. See [Nodes Reference](nodes.md) for details.

```json
"nodes": [
  {
    "id": "step1",
    "type": "llm",
    "prompt": "Do something...",
    "output_format": "text"
  }
]
```

### `routes` (required)

Defines execution flow between nodes. See [Routes Reference](routes.md).

```json
"routes": [
  {"from": "start", "to": "step1"},
  {"from": "step1", "to": "end"}
]
```

## Minimal Example

```json
{
  "version": "1.0.0",
  "goal": "Say hello",
  "nodes": [
    {
      "id": "greet",
      "type": "llm",
      "prompt": "Say hello in a creative way"
    }
  ],
  "routes": [
    {"from": "start", "to": "greet"},
    {"from": "greet", "to": "end"}
  ]
}
```

## Complete Example

See [examples/feedback-analyzer.json](../../not7-core/examples/feedback-analyzer.json) for a full multi-node agent.

---

[← Overview](overview.md) | [Nodes Reference →](nodes.md)

