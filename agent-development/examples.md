# Agent Examples & Patterns

## Pattern 1: Single Node (Simple Task)

**Use case:** Generate content, answer questions, classify text

```json
{
  "version": "1.0.0",
  "goal": "Generate haiku",
  "nodes": [
    {
      "id": "generate",
      "type": "llm",
      "prompt": "Write a haiku about AI agents"
    }
  ],
  "routes": [
    {"from": "start", "to": "generate"},
    {"from": "generate", "to": "end"}
  ]
}
```

## Pattern 2: Linear Pipeline (Multi-Step)

**Use case:** Data processing, analysis, transformation

```json
{
  "version": "1.0.0",
  "goal": "Process customer feedback",
  "nodes": [
    {
      "id": "extract",
      "type": "llm",
      "prompt": "Extract key themes from this feedback. Return JSON array.",
      "output_format": "json"
    },
    {
      "id": "analyze",
      "type": "llm",
      "prompt": "Analyze sentiment for each theme. Return JSON.",
      "input_format": "json",
      "output_format": "json"
    },
    {
      "id": "summarize",
      "type": "llm",
      "prompt": "Create executive summary from this analysis.",
      "input_format": "json",
      "output_format": "text"
    }
  ],
  "routes": [
    {"from": "start", "to": "extract"},
    {"from": "extract", "to": "analyze"},
    {"from": "analyze", "to": "summarize"},
    {"from": "summarize", "to": "end"}
  ]
}
```

**Data flow:**
```
text → extract → JSON themes → analyze → JSON analysis → summarize → text summary
```

## Pattern 3: Format Conversion

**Use case:** Transform between text, JSON, XML

```json
{
  "nodes": [
    {
      "id": "parser",
      "type": "llm",
      "prompt": "Parse this text into JSON: {name, email, message}",
      "input_format": "text",
      "output_format": "json"
    },
    {
      "id": "processor",
      "type": "llm",
      "prompt": "Process this JSON and return a formatted response",
      "input_format": "json",
      "output_format": "text"
    }
  ]
}
```

## Common Use Cases

### Content Generation
```json
{
  "goal": "Generate blog post",
  "nodes": [
    {"id": "outline", "prompt": "Create blog outline..."},
    {"id": "write", "prompt": "Write full post from outline..."},
    {"id": "polish", "prompt": "Edit and polish..."}
  ]
}
```

### Data Analysis
```json
{
  "goal": "Analyze survey responses",
  "nodes": [
    {"id": "categorize", "prompt": "Categorize responses..."},
    {"id": "stats", "prompt": "Generate statistics..."},
    {"id": "insights", "prompt": "Extract key insights..."}
  ]
}
```

### Question Answering
```json
{
  "goal": "Answer technical questions",
  "nodes": [
    {"id": "understand", "prompt": "Understand the question..."},
    {"id": "research", "prompt": "Find relevant information..."},
    {"id": "answer", "prompt": "Formulate clear answer..."}
  ]
}
```

## Tips

**Keep nodes focused:**
- One node = one responsibility
- Break complex tasks into steps

**Be explicit with formats:**
- Specify input_format and output_format
- Tell LLM exactly what format to return

**Use descriptive IDs:**
- `extract_themes` > `node1`
- `analyze_sentiment` > `step2`

**Test incrementally:**
- Start with 1 node
- Add nodes one at a time
- Verify each step

---

[← Routes](routes.md) | [API Reference →](../api-reference/rest-api.md)

