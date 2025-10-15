# Agent Development Overview

## What is an Agent?

An agent in NOT7 is a **declarative JSON file** that describes:
- **What** the agent should do (goal)
- **How** to break it into steps (nodes)
- **Flow** between steps (routes)

No code. Just configuration.

## Agent Lifecycle

```
1. Define → Write JSON spec
2. Deploy → Save to NOT7
3. Execute → Trigger runs
4. Evolve → Update spec based on results
```

## Basic Structure

```json
{
  "version": "1.0.0",
  "goal": "What this agent does",
  "nodes": [
    {
      "id": "step1",
      "type": "llm",
      "prompt": "Instructions..."
    }
  ],
  "routes": [
    {"from": "start", "to": "step1"},
    {"from": "step1", "to": "end"}
  ]
}
```

## Execution Flow

```
start → node1 → node2 → node3 → end
```

Each node:
- Receives input
- Processes it
- Produces output
- Passes to next node

## Two Ways to Run

**CLI (development):**
```bash
not7 run agent.json
```

**API (production):**
```bash
# Deploy
POST /api/v1/agents

# Run
POST /api/v1/agents/{id}/run
```

---

Next: [JSON Specification →](json-spec.md)

