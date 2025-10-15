# Single Node Examples

Simple agents with one processing step.

## Poem Generator

**File:** [poem-generator.json](../../not7-core/examples/poem-generator.json)

```json
{
  "version": "1.0.0",
  "goal": "Generate a poem about AI agents",
  "config": {
    "llm": {
      "provider": "openai",
      "model": "gpt-4",
      "temperature": 0.9
    }
  },
  "nodes": [
    {
      "id": "generate_poem",
      "type": "llm",
      "prompt": "Write a creative and inspiring poem about AI agents...",
      "output_format": "text"
    }
  ],
  "routes": [
    {"from": "start", "to": "generate_poem"},
    {"from": "generate_poem", "to": "end"}
  ]
}
```

**Run:**
```bash
not7 run examples/poem-generator.json
```

## Question Answerer

```json
{
  "version": "1.0.0",
  "goal": "Answer technical questions",
  "nodes": [
    {
      "id": "answer",
      "type": "llm",
      "prompt": "You are a helpful technical assistant. Answer the question concisely and accurately.",
      "output_format": "text"
    }
  ],
  "routes": [
    {"from": "start", "to": "answer"},
    {"from": "answer", "to": "end"}
  ]
}
```

## Text Classifier

```json
{
  "version": "1.0.0",
  "goal": "Classify text into categories",
  "nodes": [
    {
      "id": "classify",
      "type": "llm",
      "prompt": "Classify this text into one of: technical, sales, support, general. Return JSON: {\"category\": \"...\", \"confidence\": 0.0-1.0}",
      "output_format": "json"
    }
  ],
  "routes": [
    {"from": "start", "to": "classify"},
    {"from": "classify", "to": "end"}
  ]
}
```

## JSON Extractor

```json
{
  "version": "1.0.0",
  "goal": "Extract structured data from text",
  "nodes": [
    {
      "id": "extract",
      "type": "llm",
      "prompt": "Extract name, email, and message from this text. Return JSON: {\"name\": \"...\", \"email\": \"...\", \"message\": \"...\"}",
      "input_format": "text",
      "output_format": "json"
    }
  ],
  "routes": [
    {"from": "start", "to": "extract"},
    {"from": "extract", "to": "end"}
  ]
}
```

## When to Use Single Node

- Simple transformations
- Direct Q&A
- Content generation
- Classification tasks
- Data extraction

If you need multi-step reasoning, use [multi-node agents](multi-node.md).

---

[← Deployment](../deployment/server-mode.md) | [Multi-Node →](multi-node.md)

