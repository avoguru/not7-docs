# Runtime Internals

How NOT7 executes agents.

## Startup Sequence

```
1. Load not7.conf
2. Validate configuration
3. Initialize HTTP server
4. Create directories (deploy/, logs/)
5. Start file watcher (background)
6. Listen for requests
```

## Execution Flow

### One-off Execution (`not7 run agent.json`)

```
1. main.go loads config
2. spec/parser.go loads & validates JSON
3. executor/executor.go creates execution engine
4. executor finds "start" routes
5. executor executes each node in sequence
6. llm/openai.go calls OpenAI API (for LLM nodes)
7. logger/logger.go writes to console
8. Results displayed, metadata saved
```

### API Execution (`POST /agents/{id}/run`)

```
1. server/handlers.go receives request
2. server/storage.go loads agent spec from deploy/specs/
3. server/executor.go creates execution
4. logger/logger.go writes to logs/agent-*.log
5. executor executes nodes
6. server responds with JSON
```

## Component Responsibilities

### config/
Loads `not7.conf`, provides settings to all components.

### spec/
Parses and validates JSON agent specifications.

### llm/
Communicates with OpenAI API. Gets API key from config.

### executor/
Core execution engine. Runs nodes in sequence, follows routes.

### server/
HTTP API, CRUD operations, file watching.

### logger/
Structured logging to console (CLI) or files (server).

## Node Execution

```
1. Executor finds next node from routes
2. Checks node type ("llm", "tool", etc.)
3. For LLM nodes:
   - Gets prompt from node
   - Calls llm.Execute(prompt, input)
   - Receives output
4. Tracks timing and cost
5. Passes output to next node
6. Repeats until "end"
```

## Data Flow

```
Input (empty or provided)
  ↓
Node 1 (LLM processing)
  ↓
Output 1 (text/JSON/XML)
  ↓
Node 2 (receives Output 1 as input)
  ↓
Output 2
  ↓
...
  ↓
Final Output
```

Simple pipeline. No complex state management.

## File Watcher

Background goroutine that:

```
1. Every N seconds (from config)
2. Scan deploy/ directory
3. Find new .json files
4. Execute automatically
5. Move to deploy/processed/
```

Classic integration pattern.

## Logging

**CLI Mode:**
- Pretty formatted output to stdout
- For development and testing

**Server Mode:**
- Structured logs to files: `logs/agent-{timestamp}-{id}.log`
- Format: `[TIMESTAMP] [LEVEL] message`
- One file per execution
- Tail-able for real-time monitoring

---

[← Concepts](concepts.md) | [Execution Model →](execution-model.md)

