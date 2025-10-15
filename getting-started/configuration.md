# Configuration Reference

Configuration file: `not7.conf` (standard KEY=value format)

## OpenAI Settings

```
OPENAI_API_KEY=sk-your-api-key-here
```
**Required.** Your OpenAI API key.

```
OPENAI_DEFAULT_MODEL=gpt-4
```
Default model. Options: `gpt-4`, `gpt-4-turbo`, `gpt-3.5-turbo`

```
OPENAI_DEFAULT_TEMPERATURE=0.7
```
Default creativity (0.0-2.0). Lower = focused, Higher = creative.

```
OPENAI_DEFAULT_MAX_TOKENS=2000
```
Default max response length.

## Server Settings

```
SERVER_PORT=8080
```
HTTP server port.

```
SERVER_DEPLOY_DIR=./deploy
```
Directory for deployed agents and file drops.

```
SERVER_LOG_DIR=./logs
```
Directory for execution logs.

## File Watcher Settings

```
WATCHER_POLL_INTERVAL_SECONDS=2
```
How often to check deploy folder for new files (in seconds).

## Custom Config Path

```bash
NOT7_CONFIG=/path/to/custom.conf not7 run
```

Use a different config file.

## Example Config

```
# NOT7 Configuration
OPENAI_API_KEY=sk-proj-your-key-here
OPENAI_DEFAULT_MODEL=gpt-4
OPENAI_DEFAULT_TEMPERATURE=0.7
OPENAI_DEFAULT_MAX_TOKENS=2000
SERVER_PORT=8080
SERVER_DEPLOY_DIR=./deploy
SERVER_LOG_DIR=./logs
WATCHER_POLL_INTERVAL_SECONDS=2
```

---

[← Installation](installation.md) | [Quick Start →](quick-start.md)

