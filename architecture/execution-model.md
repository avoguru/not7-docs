# Execution Model

How NOT7 executes agent nodes.

## Sequential Execution (v0.1.0)

Nodes execute one after another:

```
Node1 completes → Node2 starts → Node2 completes → Node3 starts...
```

## Node Lifecycle

```
1. PENDING   - Node waiting to execute
2. RUNNING   - Node currently executing
3. COMPLETE  - Node finished successfully
4. FAILED    - Node encountered error
```

## LLM Node Execution

```
1. Executor receives node with type="llm"
2. Checks for node-specific LLM config
3. Falls back to global config if not specified
4. Builds prompt + input
5. Calls OpenAI API
6. Receives response
7. Calculates cost (tokens * pricing)
8. Returns output
9. Logs timing and cost
```

**Timing breakdown:**
```
Total: 2850ms
  ├─ Network latency: ~100ms
  ├─ LLM processing: ~2700ms
  └─ Overhead: ~50ms
```

## Error Handling

If a node fails:
```
1. Execution stops immediately
2. Error logged
3. Metadata marked as "failed"
4. Error returned to caller
```

No retry logic in v0.1.0 (roadmap feature).

## Metadata Tracking

Each execution records:

```json
{
  "executed_at": "2024-10-14T10:30:45Z",
  "execution_time_ms": 2850,
  "total_cost": 0.0275,
  "status": "success",
  "node_results": [
    {
      "node_id": "step1",
      "status": "success",
      "execution_time_ms": 2850,
      "cost": 0.0275
    }
  ]
}
```

## Future: Parallel Execution (Roadmap)

```
     Node1
    /     \
Node2A    Node2B  (execute in parallel)
    \     /
     Node3
```

Not yet implemented in v0.1.0.

---

[← Runtime](runtime.md) | [File Structure →](file-structure.md)

