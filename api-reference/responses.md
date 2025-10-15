# Response Formats

## Success Responses

### Deployment Response

```json
{
  "id": "agent-id",
  "status": "deployed",
  "goal": "Agent description"
}
```

### List Response

```json
{
  "agents": [
    {
      "id": "agent-1",
      "goal": "Do something",
      "created_at": "2024-10-14T10:30:00Z"
    }
  ],
  "count": 1
}
```

### Execution Response

```json
{
  "id": "agent-id-1697234567890",
  "status": "success",
  "goal": "Agent description",
  "output": "[Generated output]",
  "cost": 0.0234,
  "duration_ms": 2341,
  "log_file": "logs/agent-....log"
}
```

**Fields:**
- `id` - Execution ID (unique per run)
- `status` - `"success"` or `"error"`
- `goal` - Agent goal
- `output` - Final output from agent
- `cost` - Estimated cost in USD
- `duration_ms` - Execution time in milliseconds
- `log_file` - Path to execution log

## Error Responses

```json
{
  "status": "error",
  "error": "Error message",
  "id": "agent-id (if applicable)"
}
```

### Common Errors

**400 Bad Request:**
```json
{
  "status": "error",
  "error": "Invalid JSON"
}
```

**404 Not Found:**
```json
{
  "status": "error",
  "error": "Agent not found",
  "id": "missing-agent"
}
```

**409 Conflict:**
```json
{
  "status": "error",
  "error": "Agent already exists. Use PUT to update.",
  "id": "duplicate-agent"
}
```

**500 Internal Server Error:**
```json
{
  "status": "error",
  "error": "Execution failed: [details]",
  "id": "exec-id"
}
```

## HTTP Status Codes

- `200` - OK (success)
- `201` - Created (agent deployed)
- `400` - Bad Request (invalid input)
- `404` - Not Found (agent doesn't exist)
- `405` - Method Not Allowed
- `409` - Conflict (duplicate ID)
- `500` - Internal Server Error

---

[← Endpoints](endpoints.md) | [Deployment →](../deployment/server-mode.md)

