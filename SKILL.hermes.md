---
name: ralph-wiggum-loop
version: 2.0.0
description: Persistence loop until task completion with verification — Hermes Edition
compatible-with: [openclaw, hermes]
deprecated: false
supersedes: nerua1/skill-ralph
---

# Ralph Wiggum Loop — Hermes Edition

Self-referential persistence loop that keeps working until task is fully complete and verified. Prevents "almost done" syndrome.

## What It Does

1. Takes a task that must be completed
2. Attempts it, then self-reviews
3. If not fully done: retries with self-correction
4. Repeats until verified complete
5. Returns final result

## Usage (Hermes)

```bash
# Install
git clone https://github.com/nerua1/skill-ralph.git ~/.hermes/skills/ralph/

# In skill — invoke the loop pattern
# Hermes auto-loads skills matching task context
```

## Usage (OpenClaw)

```bash
openclaw skill run ralph "task description"
```

---

Built by [nerua1](https://github.com/nerua1). Support: [PayPal.me/nerudek](https://www.paypal.me/nerudek)
