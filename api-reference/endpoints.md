# API Endpoints

Quick reference for all NOT7 REST API endpoints.

## Summary Table

| Method | Endpoint | Purpose |
|--------|----------|---------|
| POST | `/api/v1/agents` | Deploy agent (save) |
| GET | `/api/v1/agents` | List all agents |
| GET | `/api/v1/agents/{id}` | Get agent spec |
| PUT | `/api/v1/agents/{id}` | Update agent |
| DELETE | `/api/v1/agents/{id}` | Delete agent |
| POST | `/api/v1/agents/{id}/run` | Execute deployed agent |
| POST | `/api/v1/agents/run` | Execute anonymous agent |
| GET | `/health` | Health check |

## Complete Workflow Example

```bash
# 1. Deploy
curl -X POST http://localhost:8080/api/v1/agents \
  -d @agent.json

# 2. List
curl http://localhost:8080/api/v1/agents

# 3. Get
curl http://localhost:8080/api/v1/agents/my-agent

# 4. Run (multiple times)
curl -X POST http://localhost:8080/api/v1/agents/my-agent/run
curl -X POST http://localhost:8080/api/v1/agents/my-agent/run
curl -X POST http://localhost:8080/api/v1/agents/my-agent/run

# 5. Update
curl -X PUT http://localhost:8080/api/v1/agents/my-agent \
  -d @agent-v2.json

# 6. Delete
curl -X DELETE http://localhost:8080/api/v1/agents/my-agent
```

## Testing Script

See [examples/crud-api-examples.sh](../../not7-core/examples/crud-api-examples.sh) for a complete test suite.

---

[← REST API](rest-api.md) | [Responses →](responses.md)

