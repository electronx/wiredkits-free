---
name: pick-model-effort
description: Choose the Claude model and effort level for a task or a subagent, so cheap work runs on cheap models and hard work gets the strong ones. Use before spawning subagents or parallel agents, when planning a multi-step task, when asked "which model should I use", or when setting up agents in .claude/agents.
---

# Pick the model and effort

The goal is accurate work without paying top-model prices for searching files. Pick the settings yourself, say what you picked when you delegate, and move on.

## 1. Sort the work

Split the task into pieces and give each one a class:

| Class | Examples | Model | Effort |
|---|---|---|---|
| **Lookup** | find files, grep, read docs, list callers, summarize logs, classify, reformat | `haiku` | low |
| **Routine build** | clear-scope edits, tests from a spec, renames, small features, docs | `sonnet` | medium |
| **Judgment** | code review, bug hunting, hard debugging, security, migrations | `opus` | high |
| **Design** | architecture, plans, research with trade-offs, decisions that are costly to undo | `opus` | xhigh |
| **Hardest** | long autonomous runs, or a problem where `opus` already fell short | `fable` | high or xhigh |

Rules of thumb:

- When unsure, go higher for decisions and lower for mechanical work.
- A stronger model at lower effort usually beats a weaker model at high effort.
- `max` effort only when being right matters far more than cost or time.
- Lookups are where most tokens go. Moving them off the main model is the biggest saving.
- Use only models your account offers. If `fable` isn't available, use `opus` at xhigh.

## 2. Know how Claude Code picks a subagent's model

First match wins:

1. `model` passed for that one call (the Agent tool's `model` parameter)
2. `model:` in the agent file (`inherit` means the main session's model)
3. The `CLAUDE_CODE_SUBAGENT_MODEL` environment variable
4. The main session's model

Effort comes from `effort:` in the agent file or the call. The `CLAUDE_CODE_EFFORT_LEVEL` environment variable overrides both.

The trap: the built-in Explore, Plan and general-purpose agents inherit the main model. Six parallel Explore agents in an Opus session are six Opus agents. Pass `model: "haiku"` when you spawn them for lookups, or use the `searcher` agent below.

## 3. Use the agents in this kit

This kit installs four agents with sensible defaults:

| Agent | Model, effort | Tools | Use for |
|---|---|---|---|
| `searcher` | haiku, low | read-only | finding code, lookups, reading many files and returning a short summary |
| `implementer` | sonnet, medium | all | routine edits and tests with a clear spec |
| `reviewer` | opus, high | read-only | reviewing a diff, hunting bugs, checking a plan |
| `architect` | opus, xhigh | read-only | design, plans, trade-offs |

If you need a different setting for one call, pass `model` (and effort, where your version supports it) on that call instead of editing the agent.

If an agent isn't installed (for example, the skill was copied without the agents), pass `model` on each Agent call using the table in step 1.

## 4. When you delegate

- In one line, say which agent or model and effort you chose and why, for example: "Searching with `searcher` (haiku), then `reviewer` (opus, high) on the diff."
- Send heavy reading to a subagent and ask for a short summary with file paths, not a dump. That keeps the main context small, which saves tokens on every later turn.
- Run independent lookups in parallel, on the cheap model.
- Don't give a subagent a decision it can't see the context for. Keep the decision in the main session and delegate the gathering.

## 5. Keep the main session cheap

- If the next task is unrelated to the last one, suggest `/clear` or a new session. A long context makes every turn cost more.
- If the user asks for something that breaks these rules (an Opus or Fable subagent at high effort for a file search, say), point it out in one line with the cheaper option, then do what they decide. Don't block them or repeat the point.
