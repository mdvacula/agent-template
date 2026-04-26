# Agent Workflow — PROJECT_NAME

This repo uses **Git Notes** (agent observability), **MCP Task Hub** (centralized
task state), **OpenSpec** (living specs), **OpenCode** and **pi** (agents, skills, commands).

## Zero-Bloat Rule

Only code and the Living Spec live in Git's file tree.
Task state lives in the remote MCP Task Hub.
Agent observability lives in `refs/notes/agent-log` — never in committed files.
Never commit JSONL, SQLite, or task markdown sidecars.

## Toolchain

| Tool | Role | Notes |
|------|------|-------|
| **Git Notes** | Agent observability | `refs/notes/agent-log` — attached to every task commit |
| **MCP Task Hub** | Task execution state | `sync_task` · `fetch_tasks` · `update_task_status` · `http://localhost:8000/sse` |
| **OpenSpec** | Living Spec + change workflow | `openspec/specs/living-spec.md` is source of truth |
| **OpenCode** | Primary coding agent | `.opencode/` — agents, skills, commands |
| **pi** | Orchestration / CI agent | `.pi/` — agents, skills, prompts |

## Git Notes Quick Reference

```bash
# Attach a note after committing (REQUIRED after every task commit)
git notes --ref=agent-log add -m '{"agent":"opencode","task_id":"TASK-42","summary":"..."}'

# Read the note on the latest commit
git notes --ref=agent-log show HEAD

# Browse all annotated commits
git log --show-notes=agent-log --oneline

# Push notes to remote
git push origin refs/notes/agent-log

# Ensure notes are fetched automatically
git config --add remote.origin.fetch '+refs/notes/*:refs/notes/*'
```

## MCP Quick Reference

| Tool | Usage |
|-------|-------|
| `fetch_tasks(status="pending")` | Get work queue |
| `fetch_tasks(id="<id>")` | Get single task |
| `update_task_status(id, "in-progress")` | Claim a task |
| `update_task_status(id, "completed")` | Close a task |
| `sync_task(id, title, status, metadata)` | Upsert task + metadata |

## Session Start

1. `fetch_tasks(status="pending")` — health check and queue
2. Parse `openspec/specs/living-spec.md` → `sync_task` for any new requirements
3. Pick top pending task

## Task Execution Loop

1. `update_task_status(id, "in-progress")`
2. Implement
3. `git commit -m "..."`
4. `git notes --ref=agent-log add -m '{"agent":"...","task_id":"...","summary":"..."}'`
5. Tests pass, build green
6. `update_task_status(id, "completed")`
7. Mark requirement done in `openspec/specs/living-spec.md`
8. `git push origin HEAD refs/notes/agent-log`

## Landing the Plane

```bash
# Zero-bloat check before every push
git diff --cached --name-only
# Remove anything ending in .jsonl .sqlite .db or task markdown sidecars

git pull --rebase
git push origin HEAD refs/notes/agent-log
git status   # MUST show "up to date with origin"
```

## Forbidden Patterns

- Committing `.jsonl`, `.sqlite`, `.db`, or task markdown sidecars
- Closing a task before the build passes
- Closing a task with uncommitted changes
- Pushing without also pushing `refs/notes/agent-log`
- Stopping before `git push` succeeds

## Agents

| Agent | Purpose |
|-------|---------|
| `hub-runner` | Execute one task end-to-end |
| `hub-orchestrator` | Coordinate parallel hub-runner subagents |
| `openspec-orchestrator` | Plan: idea → spec → tasks synced to hub |
