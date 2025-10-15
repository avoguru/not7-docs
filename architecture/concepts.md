# Core Concepts

## The Philosophy

NOT7 returns to a simpler approach: **declarative specifications** executed by a **single-binary runtime**.

### Config-Driven

Everything is configuration:
- Agent specs are JSON configs
- Runtime configured via `not7.conf`
- No programming required

### Specification-Driven

Agents are JSON specifications:

```json
{
  "nodes": [{"id": "step1", "type": "llm", "prompt": "..."}],
  "routes": [{"from": "step1", "to": "step2"}]
}
```

No code. Just configuration.

### Versioning as Intelligence

Don't build complex conditional logic upfront. Instead:

1. Run agent (v1.json)
2. Measure outcomes
3. Modify spec (v2.json)
4. Compare: `git diff v1.json v2.json`

Evolution is visible. Changes are traceable.

### Transparency as Debuggability

When execution fails:
```
1. Read the JSON spec
2. See exact node flow
3. Check prompt and configuration
4. Review logs
```

Debugging is reading configuration, not code.

### File-Based Storage

Everything is files:
- Agents: `deploy/specs/{id}.json`
- Logs: `logs/agent-{timestamp}-{id}.log`
- No database required
- Git-friendly
- Easy backup

### Stateless Execution

Each execution is independent:
- No shared state between runs
- Clean, predictable behavior
- Easy to reason about

## Design Principles

**Declarative**  
Describe WHAT, not HOW.

**Transparent**  
Readable specs, observable execution.

**File-Based**  
No database required. Everything in files.

---

[Architecture Home](../README.md#architecture) | [Runtime →](runtime.md)

