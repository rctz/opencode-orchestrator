# opencode config

Personal [opencode](https://opencode.ai) configuration with a multi-agent orchestration setup.

## Setup

1. Copy the example config and fill in your credentials:
   ```bash
   cp opencode.example.json opencode.json
   ```
2. Edit `opencode.json` and replace `YOUR_URL_HERE` and `YOUR_API_KEY_HERE` with your LiteLLM gateway URL and API key.

## Agent architecture

Work is split across four specialized agents to keep quality high and cost low.

```
User
 └── orchestrator  (Pro model — plans and coordinates)
      ├── scout    (Flash — read-only research: DeepWiki → Context7 → web)
      ├── explore  (Flash — local file lookup only)
      └── general  (Flash — file edits, shell commands, implementation)
```

### orchestrator

Primary agent. Delegates almost all work to subagents. Handles planning, coordination, and final reporting.

Operates in three modes:
- **Simple** — quick tasks, direct response
- **Build** — full 6-stage pipeline (domain mapping → context → plan → execute → review → report)
- **Continuation** — picks up from prior session context

In Build mode, the orchestrator presents its plan for user approval before any files are touched.

### scout

Read-only research agent with web access. Used for codebase surveys, documentation lookups, and code review.

Source priority: DeepWiki → Context7 → web search. Never edits files or runs commands.

### explore

Read-only local agent. Used for fast file lookups when no web access is needed.

### general

Write-capable worker agent. Handles file edits, formatting, running tests, and multi-step implementation. The only agent that runs shell commands.

## MCP tools

| Tool | Purpose |
|---|---|
| `context7` | Library documentation lookup |
| `deepwiki` | GitHub repo wiki search |
| `chrome-devtools` | Browser automation (heavy audit tools disabled) |

## Plugins

- `@warp-dot-dev/opencode-warp` — Warp terminal integration
- `@cortexkit/opencode-magic-context` — Enhanced context management
- `@slkiser/opencode-quota` — Token quota display
