# File Structure

## Overview

NOT7 uses file-based storage for executions and logs. No database required.

## Directory Layout

```
not7-core/
├── not7                # Binary
├── not7.conf           # Configuration
├── executions/         # Execution storage
│   └── {execution-id}/
│       ├── output.txt
│       ├── result.json
│       └── trace.json
└── logs/              # Execution logs
    └── {execution-id}.log
```

## Execution Storage

**Location:** `executions/{execution-id}/`

Each execution creates a directory with three files:

### output.txt

Final agent output as plain text.

```
Roses are red,
Violets are blue,
AI agents think,
In neural nets true.
```

### result.json

Complete execution metadata:

```json
{
  "id": "exec-1697234567890",
  "spec": { ... },
  "status": "completed",
  "created_at": "2024-10-18T10:30:45Z",
  "started_at": "2024-10-18T10:30:45Z",
  "ended_at": "2024-10-18T10:30:48Z",
  "result": {
    "output": "...",
    "duration_ms": 2850,
    "total_cost": 0.0275,
    "metadata": {
      "node_results": [...]
    }
  }
}
```

### trace.json

Full agent specification with execution trace:

```json
{
  "id": "poem-generator",
  "version": "1.0.0",
  "goal": "Generate a poem",
  "nodes": [...],
  "routes": [...],
  "metadata": {
    "executed_at": "2024-10-18T10:30:45Z",
    "execution_time_ms": 2850,
    "total_cost": 0.0275,
    "node_results": [
      {
        "node_id": "generate",
        "status": "success",
        "execution_time_ms": 2850,
        "cost": 0.0275,
        "output": "..."
      }
    ]
  }
}
```

For ReAct nodes, includes detailed reasoning trace:

```json
{
  "react_trace": {
    "iterations": 6,
    "thinking_steps": [
      {
        "iteration": 1,
        "thought": "I need to search for information...",
        "duration_ms": 1500,
        "cost": 0.015,
        "tool_calls": [
          {
            "tool_name": "WebSearch",
            "arguments": {"query": "..."},
            "result": [...],
            "duration_ms": 500
          }
        ]
      }
    ]
  }
}
```

## Logs

**Location:** `logs/{execution-id}.log`

Structured execution logs:

```
[2024-10-18T10:30:45Z] [INFO] Starting agent: Generate a poem
[2024-10-18T10:30:45Z] [INFO] Version: 1.0.0
[2024-10-18T10:30:45Z] [INFO] Executing node: generate (llm)
[2024-10-18T10:30:48Z] [INFO] Node generate completed in 2850ms (cost: $0.0275)
[2024-10-18T10:30:48Z] [INFO] Execution completed in 2850ms
[2024-10-18T10:30:48Z] [INFO] Total cost: $0.0275
```

For ReAct nodes with tools:

```
[2024-10-18T10:30:45Z] [INFO] Starting ReAct reasoning with tools (max iterations: 15)
[2024-10-18T10:30:45Z] [INFO] Available tools: 2
[2024-10-18T10:30:45Z] [INFO] ReAct iteration 1/15
[2024-10-18T10:30:46Z] [INFO] Tool call detected: WebSearch
[2024-10-18T10:30:47Z] [INFO] Tool executed successfully in 500ms
```

## Execution ID Format

Execution IDs are generated as:

```
{agent-id}-{timestamp}
```

Examples:
- `poem-generator-1697234567890` - Agent with ID
- `exec-1697234567890` - Anonymous execution

Timestamp is Unix nanoseconds for uniqueness.

## Storage Management

### Cleanup

Remove old executions:

```bash
# Remove executions older than 7 days
find executions/ -type d -mtime +7 -exec rm -rf {} \;
```

### Disk Usage

Check storage:

```bash
du -sh executions/
du -sh logs/
```

### Retention

NOT7 does not automatically delete executions or logs. Implement your own retention policy based on:

- Execution age
- Storage limits
- Cost thresholds

## Configuration

Set storage locations in `not7.conf`:

```ini
# Default: ./executions
SERVER_DEPLOY_DIR=./deploy

# Executions stored in: {DEPLOY_DIR}/executions
# Logs stored in: {LOG_DIR}
SERVER_LOG_DIR=./logs
```

## Concurrency

Storage operations are thread-safe:

- Atomic writes (temp file + rename)
- Read-write locks for consistency
- No database connection limits
- Parallel executions supported
