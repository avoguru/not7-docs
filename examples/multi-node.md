# Multi-Node Examples

Complex agents with multiple processing steps.

## Feedback Analyzer (3 Nodes)

**File:** [feedback-analyzer.json](../../not7-core/examples/feedback-analyzer.json)

### Flow

```
Input: Customer feedback text
  ↓
Node 1: Extract Themes → JSON array
  ↓
Node 2: Analyze Sentiment → JSON analysis
  ↓
Node 3: Generate Summary → Text report
  ↓
Output: Executive summary
```

### Complete Spec

```json
{
  "id": "feedback-analyzer",
  "version": "1.0.0",
  "goal": "Analyze customer feedback and generate actionable insights",
  
  "nodes": [
    {
      "id": "extract_themes",
      "type": "llm",
      "prompt": "Extract 3-5 key themes. Return JSON array.",
      "output_format": "json"
    },
    {
      "id": "analyze_sentiment",
      "type": "llm",
      "prompt": "Analyze sentiment for each theme. Return JSON.",
      "input_format": "json",
      "output_format": "json"
    },
    {
      "id": "generate_summary",
      "type": "llm",
      "prompt": "Create executive summary with recommendations.",
      "input_format": "json",
      "output_format": "text"
    }
  ],
  
  "routes": [
    {"from": "start", "to": "extract_themes"},
    {"from": "extract_themes", "to": "analyze_sentiment"},
    {"from": "analyze_sentiment", "to": "generate_summary"},
    {"from": "generate_summary", "to": "end"}
  ]
}
```

### Execution Result

```
Node 1: 2,160ms | $0.003 | Output: ["pricing", "performance", "support"]
Node 2: 9,932ms | $0.014 | Output: {sentiment analysis JSON}
Node 3: 4,988ms | $0.015 | Output: "Executive Summary: ..."

Total: 17,080ms | $0.032
```

## Content Pipeline (4 Nodes)

```json
{
  "goal": "Generate and polish blog content",
  "nodes": [
    {
      "id": "research",
      "type": "llm",
      "prompt": "Research key points about the topic..."
    },
    {
      "id": "outline",
      "type": "llm",
      "prompt": "Create structured outline from research..."
    },
    {
      "id": "write",
      "type": "llm",
      "prompt": "Write full blog post from outline..."
    },
    {
      "id": "polish",
      "type": "llm",
      "prompt": "Edit for clarity, grammar, and style..."
    }
  ],
  "routes": [
    {"from": "start", "to": "research"},
    {"from": "research", "to": "outline"},
    {"from": "outline", "to": "write"},
    {"from": "write", "to": "polish"},
    {"from": "polish", "to": "end"}
  ]
}
```

## Data Enrichment (3 Nodes)

```json
{
  "goal": "Enrich and validate data",
  "nodes": [
    {
      "id": "parse",
      "type": "llm",
      "prompt": "Parse this unstructured text into JSON: {name, company, role, email}",
      "output_format": "json"
    },
    {
      "id": "validate",
      "type": "llm",
      "prompt": "Validate this data. Flag any issues. Return JSON with validation results.",
      "input_format": "json",
      "output_format": "json"
    },
    {
      "id": "enrich",
      "type": "llm",
      "prompt": "Based on the company name, infer industry and company size. Add to JSON.",
      "input_format": "json",
      "output_format": "json"
    }
  ]
}
```

## Design Tips

**Break down complex tasks:**
- Instead of: "Analyze data and create report"
- Use: Node1 (analyze) → Node2 (create report)

**Match input/output formats:**
- Node outputs JSON → Next node expects JSON

**Use intermediate representations:**
- text → JSON → processed JSON → text

---

[← Single Node](single-node.md) | [Use Cases →](use-cases.md)

