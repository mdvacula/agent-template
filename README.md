# agent-template

Ready-to-clone scaffold for agentic projects using **OpenCode** and **pi**.

## Quick Start

1. Clone this repo into your new project directory
2. Open `opencode.json` and update the MCP Task Hub URL if your hub runs on a non-default port
3. Configure git notes fetch refspec: `git config --add remote.origin.fetch '+refs/notes/*:refs/notes/*'`
4. Run `npx @fission-ai/openspec@latest init --tools opencode,pi` to refresh OpenSpec files
5. Edit `openspec/specs/living-spec.md` with your project's requirements
6. Edit `openspec/config.yaml` — fill in `PROJECT_NAME`, `TECH_STACK`
7. Edit `AGENTS.md` — replace `PROJECT_NAME` references

## What's Included

| Path | Purpose |
|------|---------|
| `AGENTS.md` | Workflow reference for all agents |
| `opencode.json` | OpenCode config — MCP Task Hub endpoint |
| `.opencode/agents/` | hub-runner, hub-orchestrator, openspec-orchestrator |
| `.opencode/skills/` | OpenSpec skills + agentic-setup skill |
| `.opencode/commands/` | OpenSpec slash commands (`/opsx-*`) |
| `.pi/agents/` | hub-runner, hub-orchestrator, openspec-orchestrator |
| `.pi/skills/` | OpenSpec skills + agentic-setup skill |
| `.pi/prompts/` | OpenSpec slash commands (`/opsx-*`) |
| `openspec/specs/living-spec.md` | Living Spec starter |
| `openspec/config.yaml` | OpenSpec schema + context |

## Observability

Every task commit gets a Git Note on `refs/notes/agent-log`:

```bash
git notes --ref=agent-log add -m '{"agent":"opencode","task_id":"...","summary":"..."}'
git push origin HEAD refs/notes/agent-log
git log --show-notes=agent-log --oneline
```

No third-party service. No extra files. The "AI blame" lives inside your own repo.

## Source

Generated from [agent-kit](https://github.com/mdvacula/agent-kit) by the agentic-setup skill.
