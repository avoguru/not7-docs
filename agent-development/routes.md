# Routes Reference

Routes define the execution flow between nodes.

## Basic Route

```json
{"from": "node_a", "to": "node_b"}
```

Output of `node_a` becomes input of `node_b`.

## Special Nodes

**`"start"`** - Entry point (no incoming routes)  
**`"end"`** - Exit point (no outgoing routes)  

## Linear Pipeline

```json
{
  "nodes": [
    {"id": "step1", ...},
    {"id": "step2", ...},
    {"id": "step3", ...}
  ],
  "routes": [
    {"from": "start", "to": "step1"},
    {"from": "step1", "to": "step2"},
    {"from": "step2", "to": "step3"},
    {"from": "step3", "to": "end"}
  ]
}
```

**Flow:**
```
start → step1 → step2 → step3 → end
```

## Data Flow

Output of one node = Input of next node.

**Example:**
```
Node1: "Extract themes" → ["pricing", "performance"]
         ↓
Node2: Receives ["pricing", "performance"] as input
```

## Current Implementation (v0.1.0)

- ✅ Linear routing (A → B → C)
- ⏳ Conditional routing (if/then)
- ⏳ Parallel execution (A ‖ B ‖ C)
- ⏳ Branching & merging

## Route Fields

```json
{
  "from": "source_node_id",
  "to": "target_node_id"
}
```

Future fields (roadmap):
- `condition` - Conditional routing
- `parallel` - Parallel execution flag

## Examples

### Simple (1 node)

```json
"routes": [
  {"from": "start", "to": "generate"},
  {"from": "generate", "to": "end"}
]
```

### Pipeline (3 nodes)

```json
"routes": [
  {"from": "start", "to": "extract"},
  {"from": "extract", "to": "analyze"},
  {"from": "analyze", "to": "summarize"},
  {"from": "summarize", "to": "end"}
]
```

---

[← Nodes](nodes.md) | [Examples →](examples.md)

