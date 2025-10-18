# NOT7 Documentation

Declarative agent runtime. Single binary. Zero external dependencies.

## Overview

NOT7 executes AI agents defined as JSON specifications. Agents consist of nodes (LLM calls, ReAct loops, tools) connected by routes. The runtime handles execution, state management, and persistence.

## Quick Start

```bash
# Start server
./not7 serve

# Execute agent
./not7 run agent.json
```

See [Quick Start](getting-started/quick-start.md) for details.

## Documentation

### Getting Started
- [Installation](getting-started/installation.md) - Download and configure
- [Quick Start](getting-started/quick-start.md) - Run your first agent
- [Configuration](getting-started/configuration.md) - Environment setup

### Agent Development
- [JSON Specification](agent-development/json-spec.md) - Agent structure
- [Nodes](agent-development/nodes.md) - Execution units (LLM, ReAct, Tool)
- [Routes](agent-development/routes.md) - Node connections
- [Examples](agent-development/examples.md) - Common patterns

### API Reference
- [REST API](api-reference/rest-api.md) - HTTP endpoints
- [Responses](api-reference/responses.md) - Return formats

### Architecture
- [Execution Model](architecture/execution-model.md) - How agents run
- [File Structure](architecture/file-structure.md) - Directory layout
- [Runtime](architecture/runtime.md) - Internal components

### Deployment
- [Server Mode](deployment/server-mode.md) - Production deployment

## Repository

- [NOT7 Core (Go)](https://github.com/not7/core)
- [NOT7 Docs](https://github.com/not7/docs)
