# Runtime Architecture

## Overview

NOT7 runtime consists of a unified execution engine that handles all agent executions through a single code path, whether initiated via CLI or HTTP API.

## Architecture Diagram

```
            ┌───────────────────────────────┐
            │     USER / DEVELOPER          │
            └───────────────┬───────────────┘
                            ↓
            ┌───────────────────────────────┐
            │   JSON Agent Definition       │
            │   (nodes, routes, config)     │
            └───────────────┬───────────────┘
                            ↓
            ┌───────────────────────────────┐
            │   NOT7 CORE RUNTIME           │
            │   Executor Engine             │
            └───────────────┬───────────────┘
                            ↓
        ┌───────────────────┼───────────────────┐
        ↓                   ↓                   ↓
┌───────────────┐  ┌────────────────┐  ┌───────────────┐
│ LLM Provider  │  │ Tool Providers │  │    Storage    │
│               │  │                │  │               │
│ - OpenAI      │  │ - Native       │  │ - Executions  │
│ - GPT-4/3.5   │  │ - Arcade       │  │ - Traces      │
│               │  │   (Gmail, Maps)│  │ - Logs        │
└───────────────┘  └────────────────┘  └───────────────┘
```

## Core Components

```
┌─────────────┐        ┌─────────────┐
│   CLI       │        │  HTTP API   │
│  Commands   │        │  Handlers   │
└──────┬──────┘        └──────┬──────┘
       │                      │
       └──────────┬───────────┘
                  ▼
         ┌────────────────┐
         │ execution.     │  ← Single source of truth
         │ Manager        │
         └────────┬───────┘
                  │
       ┌──────────┴──────────┐
       ▼                     ▼
┌──────────────┐      ┌──────────────┐
│  executor.   │      │  execution.  │
│  Executor    │      │  Storage     │
└──────┬───────┘      └──────┬───────┘
       │                     │
       ▼                     ▼
┌──────────────┐      ┌──────────────┐
│  llm.        │      │ executions/  │
│  OpenAI      │      │  {id}/       │
└──────────────┘      └──────────────┘
```

## Package Structure

### execution/

Domain package for execution orchestration:

- **types.go** - Domain models (Execution, Result, Status, Options)
- **manager.go** - Thread-safe execution orchestration
- **storage.go** - Storage interface + FileSystemStorage implementation
- **errors.go** - Domain errors

### executor/

Execution engine for agent processing:

- **executor.go** - Node execution, routing, state management
- **react.go** - ReAct loop implementation

### llm/

LLM provider integration:

- **openai.go** - OpenAI API client with cost tracking

### server/

HTTP server:

- **server.go** - HTTP setup, routing
- **handlers.go** - API endpoints

### cmd/

CLI commands:

- **run.go** - Execute agent
- **serve.go** - Start server
- **status.go** - Check execution status

## Execution Flow

### CLI Execution

```
./not7 run agent.json
  ↓
cmd/run.go
  ↓
client.RunAgent() → HTTP POST /api/v1/run
  ↓
server/handlers.go
  ↓
execution.Manager.Execute()
  ↓
┌─────────────────────────────┐
│ 1. Create Execution         │
│ 2. Validate spec            │
│ 3. Save initial state       │
│ 4. Mark as running          │
│ 5. Create logger            │
│ 6. Execute via executor     │
│ 7. Mark complete/failed     │
│ 8. Save final state         │
│ 9. Save output + trace      │
└─────────────────────────────┘
  ↓
Response to CLI
```

### API Execution

```
POST /api/v1/run
  ↓
server/handlers.go
  ↓
execution.Manager.Execute(spec, options)
  ↓
[Same execution path as CLI]
  ↓
JSON response
```

## Execution Modes

### Synchronous

- Blocks until completion
- Returns full result
- `options.Async = false`

### Asynchronous

- Returns immediately with execution ID
- Runs in background goroutine
- `options.Async = true`
- Check status via `/api/v1/executions/{id}`

### Trace Mode

- Live ReAct reasoning output
- `options.Trace = true`
- Streams thinking steps to client

## State Management

### Execution Lifecycle

```
pending → running → completed
                  ↘ failed
```

**State Transitions:**

1. **pending** - Created, queued for execution
2. **running** - Actively executing nodes
3. **completed** - Successfully finished
4. **failed** - Error occurred

### Persistence

All state persisted to filesystem:

```
executions/{id}/
├── result.json   # Execution + Result
├── output.txt    # Final output
└── trace.json    # Agent spec + metadata
```

Atomic writes ensure consistency:
1. Write to temp file
2. Rename to final location
3. No partial/corrupted files

## Concurrency

### Thread Safety

- **sync.Map** - Active executions tracking
- **sync.RWMutex** - Storage operations
- Parallel executions fully supported
- No shared mutable state

### Goroutines

- Async executions run in background goroutines
- Each execution isolated
- No execution limits (OS-bound)

## Node Execution

```
execution.Manager.Execute()
  ↓
executor.Executor.Execute()
  ↓
┌────────────────────────────┐
│ 1. Find routes from "start"│
│ 2. For each node:          │
│    a. Execute node         │
│    b. Track time + cost    │
│    c. Pass output to next  │
│ 3. Follow routes           │
│ 4. Repeat until "end"      │
└────────────────────────────┘
```

### Node Types

**LLM Node:**
```go
executor.executeLLMNode()
  → llm.OpenAIClient.Execute()
  → HTTP call to OpenAI
  → Return output + cost
```

**ReAct Node:**
```go
executor.executeReActNode()
  → Loop until "FINAL:" or max_iterations
  → Each iteration: LLM call
  → Track thinking steps
  → Return final answer + trace
```

**Tool Node (with ReAct):**
```go
executor.executeReActNodeWithTools()
  → ReAct loop with tool access
  → Parse tool calls from LLM
  → Execute tools (WebSearch, WebFetch)
  → Feed results back to LLM
  → Continue reasoning
```

## Cost Tracking

Token-based pricing calculated per node:

```go
cost = (prompt_tokens / 1000 * input_rate) +
       (completion_tokens / 1000 * output_rate)
```

Aggregated across all nodes for total execution cost.

## Error Handling

### Validation Errors

- Spec parsing failures
- Missing required fields
- Invalid node/route references

Return `400 Bad Request`

### Execution Errors

- LLM API failures
- Tool execution errors
- Node processing errors

Mark execution as `failed`, save error in result.

### Storage Errors

- File system failures
- Disk full

Return `500 Internal Server Error`

## Logging

### File Logs

**Location:** `logs/{execution-id}.log`

**Format:**
```
[2024-10-18T10:30:45Z] [INFO] Message
[2024-10-18T10:30:45Z] [ERROR] Error message
```

**One file per execution** - easy to correlate with execution ID.

### Log Levels

- **INFO** - Normal operations
- **ERROR** - Failures, exceptions
- **DEBUG** - Detailed traces (not implemented)

## Configuration

**File:** `not7.conf`

**Required:**
- `OPENAI_API_KEY` - OpenAI authentication
- `OPENAI_DEFAULT_MODEL` - Model to use

**Optional:**
- `SERVER_PORT` - HTTP port (default: 8080)
- `SERVER_DEPLOY_DIR` - Storage path (default: ./deploy)
- `SERVER_LOG_DIR` - Log path (default: ./logs)
- `SERP_API_KEY` - For WebSearch tool

## Performance

### Latency

- HTTP overhead: ~1-5ms
- Execution setup: ~5-10ms
- Node execution: LLM-dependent (1-10s+)
- File writes: ~1-5ms

### Throughput

Limited by:
- OpenAI API rate limits
- File system I/O
- Available memory for goroutines

No artificial concurrency limits imposed.

### Scalability

Single binary, stateless execution:
- Horizontal scaling: Run multiple instances
- Vertical scaling: More CPU/memory = more parallel executions
- No database bottleneck
- File-based storage easily distributed (NFS, S3)
