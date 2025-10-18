# API Endpoints

## Overview

NOT7 exposes a simple REST API for agent execution and status tracking.

Base URL: `http://localhost:8080/api/v1`

## Endpoints

| Method | Endpoint | Purpose |
|--------|----------|---------|
| POST | `/run` | Execute agent |
| GET | `/executions` | List all executions |
| GET | `/executions/{id}` | Get execution details |
| GET | `/health` | Health check |

## Execute Agent

**Endpoint:** `POST /api/v1/run`

**Request:**

```bash
curl -X POST http://localhost:8080/api/v1/run \
  -H "Content-Type: application/json" \
  -d @agent.json
```

**Query Parameters:**

- `async=true` - Run in background (returns immediately)
- `trace=true` - Enable live ReAct trace output

**Response (Synchronous):**

```json
{
  "id": "exec-1697234567890",
  "status": "completed",
  "goal": "Generate a poem",
  "output": "Roses are red...",
  "duration_ms": 2850,
  "total_cost": 0.0275,
  "created_at": "2024-10-18T10:30:45Z",
  "started_at": "2024-10-18T10:30:45Z",
  "ended_at": "2024-10-18T10:30:48Z"
}
```

**Response (Asynchronous):**

```json
{
  "execution_id": "exec-1697234567890",
  "status": "pending",
  "message": "Execution started in background"
}
```

## List Executions

**Endpoint:** `GET /api/v1/executions`

**Request:**

```bash
curl http://localhost:8080/api/v1/executions
```

**Response:**

```json
{
  "executions": [
    {
      "id": "exec-1697234567890",
      "goal": "Generate a poem",
      "status": "completed",
      "created_at": "2024-10-18T10:30:45Z",
      "duration_ms": 2850,
      "total_cost": 0.0275
    },
    {
      "id": "exec-1697234567891",
      "goal": "Analyze feedback",
      "status": "running",
      "created_at": "2024-10-18T10:31:00Z"
    }
  ],
  "count": 2
}
```

## Get Execution Details

**Endpoint:** `GET /api/v1/executions/{id}`

**Request:**

```bash
curl http://localhost:8080/api/v1/executions/exec-1697234567890
```

**Response:**

```json
{
  "id": "exec-1697234567890",
  "status": "completed",
  "goal": "Generate a poem",
  "output": "Roses are red...",
  "duration_ms": 2850,
  "total_cost": 0.0275,
  "created_at": "2024-10-18T10:30:45Z",
  "started_at": "2024-10-18T10:30:45Z",
  "ended_at": "2024-10-18T10:30:48Z",
  "metadata": {
    "node_results": [
      {
        "node_id": "generate",
        "status": "success",
        "execution_time_ms": 2850,
        "cost": 0.0275
      }
    ]
  }
}
```

## Health Check

**Endpoint:** `GET /health`

**Request:**

```bash
curl http://localhost:8080/health
```

**Response:**

```json
{
  "status": "healthy",
  "server": "NOT7"
}
```

## Error Responses

All errors return standard format:

```json
{
  "status": "error",
  "error": "Error message here"
}
```

Common HTTP status codes:

- `400` - Bad Request (invalid JSON, validation failed)
- `404` - Not Found (execution ID doesn't exist)
- `500` - Internal Server Error (execution failed)

## Execution Status

Status values:

- `pending` - Queued for execution
- `running` - Currently executing
- `completed` - Finished successfully
- `failed` - Execution failed

## Examples

### Synchronous Execution

```bash
curl -X POST http://localhost:8080/api/v1/run \
  -H "Content-Type: application/json" \
  -d '{
    "version": "1.0.0",
    "goal": "Generate a poem",
    "nodes": [{
      "id": "generate",
      "type": "llm",
      "prompt": "Write a short poem about AI"
    }],
    "routes": [
      {"from": "start", "to": "generate"},
      {"from": "generate", "to": "end"}
    ]
  }'
```

### Async Execution with Status Check

```bash
# Submit
EXEC_ID=$(curl -X POST "http://localhost:8080/api/v1/run?async=true" \
  -H "Content-Type: application/json" \
  -d @agent.json | jq -r '.execution_id')

# Check status
curl http://localhost:8080/api/v1/executions/$EXEC_ID
```

### List Recent Executions

```bash
curl http://localhost:8080/api/v1/executions | jq '.executions[:5]'
```
