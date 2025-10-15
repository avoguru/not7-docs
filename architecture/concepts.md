# Core Concepts

## The Philosophy

NOT7 returns to a simpler approach: **declarative specifications** executed by a **single-binary runtime**.

### Config-Driven

Everything is configuration:
- Agent specs are JSON configs
- Runtime configured via `not7.conf`
- No programming required

### Spec-Driven vs Code-Driven

**Code-driven (LangGraph, LangChain):**
```python
def my_agent(state):
    result = llm.invoke(prompt)
    return {"output": result}
    
graph.add_node("step1", my_agent)
graph.add_edge("step1", "step2")
```

**Spec-driven (NOT7):**
```json
{
  "nodes": [{"id": "step1", "type": "llm", "prompt": "..."}],
  "routes": [{"from": "step1", "to": "step2"}]
}
```

Same functionality. No code.

### Versioning as Intelligence

Don't build complex conditional logic upfront. Instead:

1. Run agent (v1.json)
2. Measure outcomes
3. Modify spec (v2.json)
4. Compare: `git diff v1.json v2.json`

Evolution is visible. Changes are traceable.

### Transparency as Debuggability

**Black box:**
```
Agent fails → Debug Python code → Find hidden state issue
```

**Transparent:**
```
Agent fails → Read JSON spec → See exact flow → Fix prompt
```

When something fails, you read the spec, not code.

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

## Learning from Integration Platforms

NOT7 brings ESB/ETL philosophy to agents:

**What worked:**
- Transparent XML/JSON definitions
- File-based configuration
- Observable execution
- Version control friendly

**What we evolved:**
- JSON (simpler than XML)
- LLM-native design
- API-first architecture
- Single binary (easier than Java)

## The NOT7 Approach

**Simple > Complex**  
Linear pipelines, not complex graphs.

**Transparent > Clever**  
Readable specs, not hidden logic.

**Declarative > Imperative**  
Describe WHAT, not HOW.

---

[Architecture Home](../README.md#architecture) | [Runtime →](runtime.md)

