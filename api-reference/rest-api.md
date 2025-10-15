# REST API Reference

NOT7 provides a complete REST API for managing and executing agents.

## Base URL

```
http://localhost:8080/api/v1
```

## Agent Management

### Deploy Agent

```
POST /api/v1/agents
```

Save agent spec without executing.

**Request:**
```bash
curl -X POST http://localhost:8080/api/v1/agents \
  -H "Content-Type: application/json" \
  -d '{
    "id": "my-agent",
    "version": "1.0.0",
    "goal": "Do something",
    "nodes": [...],
    "routes": [...]
  }'
```

**Response (201 Created):**
```json
{
  "id": "my-agent",
  "status": "deployed",
  "goal": "Do something"
}
```

**Errors:**
- 400 - Invalid JSON or missing required fields
- 409 - Agent ID already exists

### List Agents

```
GET /api/v1/agents
```

**Request:**
```bash
curl http://localhost:8080/api/v1/agents
```

**Response (200 OK):**
```json
{
  "agents": [
    {
      "id": "my-agent",
      "goal": "Do something",
      "created_at": "2024-10-14T10:30:00Z"
    }
  ],
  "count": 1
}
```

### Get Agent

```
GET /api/v1/agents/{id}
```

**Request:**
```bash
curl http://localhost:8080/api/v1/agents/my-agent
```

**Response (200 OK):**
```json
{
  "id": "my-agent",
  "version": "1.0.0",
  "goal": "Do something",
  "nodes": [...],
  "routes": [...]
}
```

**Errors:**
- 404 - Agent not found

### Update Agent

```
PUT /api/v1/agents/{id}
```

**Request:**
```bash
curl -X PUT http://localhost:8080/api/v1/agents/my-agent \
  -H "Content-Type: application/json" \
  -d '{
    "version": "2.0.0",
    "goal": "Updated goal",
    "nodes": [...],
    "routes": [...]
  }'
```

**Response (200 OK):**
```json
{
  "id": "my-agent",
  "status": "updated",
  "goal": "Updated goal"
}
```

**Errors:**
- 404 - Agent not found
- 400 - Invalid JSON

### Delete Agent

```
DELETE /api/v1/agents/{id}
```

**Request:**
```bash
curl -X DELETE http://localhost:8080/api/v1/agents/my-agent
```

**Response (200 OK):**
```json
{
  "id": "my-agent",
  "status": "deleted"
}
```

**Errors:**
- 404 - Agent not found

## Agent Execution

### Execute Deployed Agent

```
POST /api/v1/agents/{id}/run
```

Execute a deployed agent by ID. Can be called multiple times.

**Request:**
```bash
curl -X POST http://localhost:8080/api/v1/agents/my-agent/run
```

**Response (200 OK):**
```json
{
  "id": "my-agent-1697234567890",
  "status": "success",
  "goal": "Do something",
  "output": "[Agent output here]",
  "cost": 0.0234,
  "duration_ms": 2341,
  "log_file": "logs/agent-20241014-103045-my-agent-1697234567890.log"
}
```

**Errors:**
- 404 - Agent not found
- 500 - Execution failed

### Execute Anonymous Agent

```
POST /api/v1/agents/run
```

Execute agent without saving.

**Request:**
```bash
curl -X POST http://localhost:8080/api/v1/agents/run \
  -H "Content-Type: application/json" \
  -d '{
    "version": "1.0.0",
    "goal": "One-time task",
    "nodes": [...],
    "routes": [...]
  }'
```

**Response (200 OK):**
```json
{
  "id": "anon-1697234567890",
  "status": "success",
  "goal": "One-time task",
  "output": "[Agent output]",
  "cost": 0.0234,
  "duration_ms": 2341,
  "log_file": "logs/..."
}
```

## Monitoring

### Health Check

```
GET /health
```

**Request:**
```bash
curl http://localhost:8080/health
```

**Response (200 OK):**
```json
{
  "status": "healthy",
  "server": "NOT7"
}
```

## Error Responses

All errors follow this format:

```json
{
  "status": "error",
  "error": "Error message here",
  "id": "agent-id (if applicable)"
}
```

**Common HTTP Status Codes:**
- 200 - Success
- 201 - Created
- 400 - Bad Request (invalid JSON, missing fields)
- 404 - Not Found (agent doesn't exist)
- 405 - Method Not Allowed
- 409 - Conflict (agent ID already exists)
- 500 - Internal Server Error

---

[← Examples](../agent-development/examples.md) | [Endpoints →](endpoints.md)

