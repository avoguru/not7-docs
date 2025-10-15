# Real-World Use Cases

Practical applications for NOT7 agents.

## Customer Support

**Ticket Triage Agent**

```
1. Classify urgency (critical/high/low)
2. Extract key information
3. Generate response template
```

**Deployment:**
- Deploy once: `POST /agents`
- Trigger per ticket: `POST /agents/ticket-triage/run`

## Content Operations

**Daily Newsletter Agent**

```
1. Summarize top news
2. Extract key points
3. Generate newsletter copy
```

**Deployment:**
- Deploy with `schedule: "24h"`
- Runs automatically every day

## Data Processing

**Survey Analysis Agent**

```
1. Parse responses
2. Categorize by theme
3. Generate statistics
4. Create insights report
```

**Use:** Process batches of survey data.

## Business Intelligence

**Competitive Analysis Agent**

```
1. Extract competitor mentions
2. Analyze sentiment
3. Identify trends
4. Generate strategic insights
```

## Developer Productivity

**Code Documentation Agent**

```
1. Analyze code structure
2. Extract key functions
3. Generate documentation
```

## Marketing

**Social Media Post Generator**

```
1. Extract key message
2. Adapt tone for platform
3. Generate variations
```

## Research

**Literature Review Agent**

```
1. Summarize papers
2. Extract methodologies
3. Compare approaches
4. Synthesize findings
```

## Operations

**Log Analysis Agent**

```
1. Parse error logs
2. Categorize by type
3. Identify patterns
4. Suggest fixes
```

## Why NOT7 for These Use Cases?

**Repeatable:**
- Deploy once, run many times
- Consistent results

**Versionable:**
- Improve prompts over time
- Track what changed (git diff)

**Transparent:**
- Read the JSON, understand the process
- Debug by reading specs

**API-driven:**
- Integrate with existing systems
- Trigger from CI/CD, webhooks, cron

---

[← Multi-Node](multi-node.md) | [Back to Docs Home](../README.md)

