# File Structure

NOT7 uses a simple file-based structure.

## Runtime Directories

```
not7-core/
├── not7.conf              # Configuration (you create this)
├── not7                   # Binary
├── deploy/
│   ├── specs/            # Deployed agents (via API)
│   │   └── {id}.json
│   └── processed/        # File-drop executions
│       └── {filename}.json
└── logs/
    └── agent-{timestamp}-{id}.log
```

## Deployed Agents

**Location:** `deploy/specs/{id}.json`

Created via:
```bash
POST /api/v1/agents
```

Example: `deploy/specs/poem-generator.json`

## Execution Logs

**Location:** `logs/agent-{timestamp}-{id}.log`

**Naming pattern:**
```
agent-20241014-103045-poem-generator-1697234567890.log
       ↑           ↑        ↑                ↑
    date        time    agent-id      execution-id
```

**Format:**
```
[2024-10-14T10:30:45Z] [INFO] Starting agent: Generate poem
[2024-10-14T10:30:45Z] [INFO] Executing node: generate_poem (llm)
[2024-10-14T10:30:48Z] [INFO] Node completed in 2850ms (cost: $0.0275)
```

## File Drop Processing

**Location:** `deploy/`

Drop any `.json` file here:
```bash
cp agent.json deploy/
```

File watcher:
1. Detects new file
2. Executes agent
3. Moves to `deploy/processed/`

---

[← Execution Model](execution-model.md) | [Deployment →](../deployment/server-mode.md)

