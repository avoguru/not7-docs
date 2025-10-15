# Server Mode

Run NOT7 as a production server.

## Start Server

```bash
not7 run
```

Server starts on port 8080 (configurable in `not7.conf`).

## Server Output

```
NOT7 - Agent Runtime Server
===========================

🚀 Server listening on http://localhost:8080
📁 Specs directory: ./deploy/specs
📁 Deploy directory: ./deploy
📁 Logs directory: ./logs

📖 API Endpoints:
   POST   /api/v1/agents          - Deploy agent
   GET    /api/v1/agents          - List agents
   ...

👀 Watching ./deploy for new agent specs...
```

## Deployment Methods

### 1. HTTP API

```bash
curl -X POST http://localhost:8080/api/v1/agents \
  -H "Content-Type: application/json" \
  -d @agent.json
```

### 2. File Drop

```bash
cp agent.json deploy/
```

Server automatically executes and moves to `deploy/processed/`.

## Triggering Execution

**Deployed agent:**
```bash
POST /api/v1/agents/{id}/run
```

**Anonymous (no save):**
```bash
POST /api/v1/agents/run -d @agent.json
```

## Viewing Logs

**Real-time:**
```bash
tail -f logs/agent-*.log
```

**Specific execution:**
```bash
tail logs/agent-20241014-103045-my-agent-*.log
```

**Latest:**
```bash
ls -lt logs/ | head -5
```

## Configuration

Edit `not7.conf`:

```
SERVER_PORT=3000
SERVER_DEPLOY_DIR=/var/agents
SERVER_LOG_DIR=/var/log/not7
```

Restart server to apply changes.

## Running in Background

**Using nohup:**
```bash
nohup ./not7 run > server.log 2>&1 &
```

**Using systemd:** (Linux)
```ini
[Unit]
Description=NOT7 Agent Runtime

[Service]
ExecStart=/usr/local/bin/not7 run
WorkingDirectory=/opt/not7
Restart=always

[Install]
WantedBy=multi-user.target
```

**Using pm2:** (if you have Node.js)
```bash
pm2 start "./not7 run" --name not7-server
```

## Health Monitoring

```bash
curl http://localhost:8080/health
```

Returns `{"status": "healthy"}` if running.

## Stopping Server

```bash
pkill -f "not7 run"
```

Or find PID and kill:
```bash
ps aux | grep "not7 run"
kill <PID>
```

---

[← File Structure](../architecture/file-structure.md) | [Examples →](../examples/single-node.md)

