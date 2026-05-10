---
name: ralph-wiggum-loop
version: 2.1.0
description: Persistence loop until task completion with verification — Hermes Edition
compatible-with: hermes-agent
supersedes: nerudek/skill-ralph
---

# Ralph Wiggum Loop — Hermes Edition

Persistence wrapper that keeps working on a task until fully complete and verified. Prevents "almost done" syndrome using Hermes-native `delegate_task` and `todo` tools.

## What It Does

1. Takes a task that must be completed
2. Works on it iteratively (up to 10 iterations)
3. Verifies completion with fresh evidence
4. If not done: fixes issues and repeats
5. If done: clean exit with final report

## Usage (Hermes)

### The Loop Pattern

```python
# Use todo tool to track progress
todo(todos=[{
    "id": "ralph-task",
    "content": "Task description with clear completion criteria",
    "status": "in_progress"
}])

# Each iteration: delegate work, verify, decide
for iteration in range(1, 11):
    # Delegate work to subagent
    result = delegate_task(
        goal="Complete: <task>",
        context="Previous attempt results: <context>",
        toolsets=['terminal', 'file', 'web']
    )
    
    # Verify completion
    build_ok = terminal("npm run build 2>&1")["exit_code"] == 0
    tests_ok = terminal("npm test 2>&1")["exit_code"] == 0
    todos_left = search_files(pattern='TODO|FIXME', target='content', output_mode='count')
    
    if build_ok and tests_ok and not todos_left:
        todo(todos=[{"id": "ralph-task", "content": "...", "status": "completed"}])
        break
```

### State Tracking

Maintain state via `memory()` tool:

```
memory(action='add', target='memory', content='Ralph iteration N: build=pass, tests=fail (3 failures in auth.test.ts), todos=2 remaining')
```

### Completion Criteria

Task is done ONLY when ALL are true:
- Build succeeds (`npm run build` exits 0)
- Tests pass (`npm test` exits 0)
- Zero TODO/FIXME items in codebase
- Subagent reports success

## When to Use

Use Ralph when:
- Task requires guaranteed completion (not "do your best")
- Work may span multiple attempts
- Clear completion criteria exist (tests, builds)
- You need verification before declaring done

Do NOT use when:
- Quick one-shot fix needed
- Task is exploratory (research, planning)
- No clear completion criteria

## Stop Conditions

- All verification passed (success)
- Max iterations (10) reached
- User says stop/cancel/abort
- Fundamental blocker (missing credentials, external service down)

## Hermes-Native Equivalents

| OpenClaw | Hermes |
|----------|--------|
| `openclaw sessions spawn` | `delegate_task(goal=...)` |
| `openclaw state/ralph/` | `memory()` for state + `todo()` for tracking |
| `/cancel` | `process(action='kill')` |
| Verification script | `terminal()` + `search_files()` |

---

Built by [nerudek](https://github.com/nerudek). Support: [PayPal.me/nerudek](https://www.paypal.me/nerudek)
