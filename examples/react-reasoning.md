# ReAct: Iterative Reasoning

ReAct nodes enable multi-step reasoning with self-critique and refinement.

## How ReAct Works

```
Iteration 1: Think → Answer → Critique
Iteration 2: Refine → Better Answer → Critique
Iteration 3: Refine → Even Better → Critique
...
Until: Satisfied (FINAL:) OR max iterations reached
```

## Basic Example: Problem Solver

**File:** [problem-solver.json](../../not7-core/examples/problem-solver.json)

```json
{
  "id": "problem-solver",
  "version": "1.0.0",
  "goal": "Solve complex problems with step-by-step reasoning",
  
  "nodes": [
    {
      "id": "solve",
      "type": "react",
      "react_goal": "Solve the river crossing puzzle: A farmer with fox, chicken, and grain...",
      "max_iterations": 4
    }
  ],
  
  "routes": [
    {"from": "start", "to": "solve"},
    {"from": "solve", "to": "end"}
  ]
}
```

**Run:**
```bash
not7 run examples/problem-solver.json
```

**Result:**
- 4 iterations of thinking
- 47 seconds total
- $0.114 cost
- Correct solution with step-by-step reasoning

## Execution Trace

The result file includes complete thinking trace:

```json
{
  "react_trace": {
    "iterations": 4,
    "thinking_steps": [
      {
        "iteration": 1,
        "thought": "THINK: The farmer needs to...",
        "duration_ms": 9737,
        "cost": 0.027
      },
      {
        "iteration": 2,
        "thought": "CONTINUE: Need to refine...",
        "duration_ms": 10498,
        "cost": 0.028
      },
      ...
    ],
    "total_thinking_time_ms": 47320,
    "iterations_cost": 0.1142
  }
}
```

## Use Cases for ReAct

**Complex Problem Solving:**
- Logic puzzles
- Strategic planning
- System design
- Algorithm design

**Deep Analysis:**
- Multi-faceted topics
- Comparing alternatives
- Evaluating trade-offs

**Creative Tasks:**
- Story outlines with refinement
- Architectural proposals
- Content planning

## ReAct vs Simple LLM

**Use LLM node when:**
- Single-pass processing
- Clear, straightforward task
- Speed matters more than depth

**Use ReAct node when:**
- Complex reasoning required
- Need to explore multiple angles
- Quality > Speed
- Self-correction valuable

## Configuration

```json
{
  "type": "react",
  "react_goal": "Your research/problem goal",
  "max_iterations": 5,  // Balance speed vs thoroughness
  "llm": {
    "model": "gpt-4",
    "temperature": 0.7  // Higher for creative thinking
  }
}
```

**Iteration guidelines:**
- 3-4: Quick reasoning
- 5-6: Balanced (recommended)
- 8-10: Deep exploration

## Viewing Results

**Final answer:**
```bash
cat examples/problem-solver.json.result.json | jq -r '.metadata.node_results[0].output'
```

**Thinking trace:**
```bash
cat examples/problem-solver.json.result.json | jq '.metadata.node_results[0].react_trace'
```

**Cost breakdown:**
```bash
cat examples/problem-solver.json.result.json | jq '.metadata.node_results[0].react_trace.thinking_steps[] | {iteration, cost, duration_ms}'
```

---

[← Multi-Node](multi-node.md) | [Use Cases →](use-cases.md)

