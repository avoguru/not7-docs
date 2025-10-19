# Quick Start

## Prerequisites

- Go 1.21+ (to build from source)
- OpenAI API key
- macOS, Linux, or Windows

## Installation

### Option 1: Download Binary

```bash
# macOS (ARM)
curl -L https://github.com/not7/core/raw/main/dist/not7-darwin-arm64 -o not7
chmod +x not7

# macOS (Intel)
curl -L https://github.com/not7/core/raw/main/dist/not7-darwin-amd64 -o not7
chmod +x not7

# Linux
curl -L https://github.com/not7/core/raw/main/dist/not7-linux-amd64 -o not7
chmod +x not7
```

### Option 2: Build from Source

```bash
git clone https://github.com/not7/core
cd not7-core
make build
```

## Configuration

Create `not7.conf`:

```bash
cp not7.conf.example not7.conf
```

Edit with your API key:

```ini
# NOT7 Configuration
OPENAI_API_KEY=sk-your-key-here
OPENAI_DEFAULT_MODEL=gpt-4
SERVER_PORT=8080
```

## Run Your First Agent

### Terminal 1: Start Server

```bash
./not7 serve
```

Output:
```
🚀 Server listening on http://localhost:8080
📁 Executions: ./executions
📁 Logs: ./logs
```

### Terminal 2: Execute Agent

```bash
./not7 run examples/poem-generator.json
```

Output shows the agent execution with timing and cost.

## Try Arcade Integration (Google Services)

**NEW:** Try the multi-toolkit demo using Google Maps + Gmail:

### Setup Arcade

Add to your `not7.conf`:

```ini
# Arcade.dev Integration
ARCADE_API_KEY=your-api-key-here
ARCADE_USER_ID=your-user-id-here
```

Get your credentials from [arcade.dev](https://arcade.dev)

### Authorize Google Services

Run the one-time interactive OAuth authorization:

```bash
./not7 authorize
```

This will display a URL - open it in your browser to authorize Google services.

### Run Multi-Toolkit Demo

**Important:** First edit `examples/arcade-lunch-demo.json` and update the email recipient from `user@example.com` to your actual email address!

```bash
./not7 run examples/arcade-lunch-demo.json
```

This agent will:
1. Use Google Maps to find 3 lunch restaurant recommendations
2. Format them nicely
3. Use Gmail to send you an email with the recommendations

The agent uses **2 different toolkits in one workflow** - demonstrating per-node toolkit configuration!

## Execution Modes

### Synchronous (Default)

Blocks until complete:

```bash
./not7 run agent.json
```

### Asynchronous

Returns immediately with execution ID:

```bash
./not7 run agent.json --async
```

Check status:

```bash
./not7 status <execution-id>
```

### With Live Streaming

Stream live agent reasoning (for debugging):

```bash
./not7 run agent.json --stream
```

## Via HTTP API

Execute via cURL:

```bash
curl -X POST http://localhost:8080/api/v1/run \
  -H "Content-Type: application/json" \
  -d @agent.json
```

Async execution:

```bash
curl -X POST "http://localhost:8080/api/v1/run?async=true" \
  -H "Content-Type: application/json" \
  -d @agent.json
```

## Output Files

After execution, find results in:

```
executions/<execution-id>/
├── output.txt    # Final agent output
└── trace.json    # Agent spec + execution metadata + trace
```

Logs:

```
logs/agent-<timestamp>-<id>.log
```

## Next Steps

- [Agent Development](../agent-development/json-spec.md) - Create custom agents
- [API Reference](../api-reference/rest-api.md) - HTTP API details
- [Examples](../agent-development/examples.md) - Common patterns
