# async-vibes

Orchestrator pattern for AI coding agents. Teaches your agent to delegate work to sub-agents instead of writing code directly.

## What This Skill Does

Most AI coding agents default to doing everything themselves — reading files, writing code, running tests — sequentially in one context window. This skill rewires that behavior into an **orchestrator pattern**: break tasks into work units, delegate to specialized sub-agents, and parallelize aggressively.

Your agent learns:

- **When to delegate** vs. when to work directly
- **How to delegate effectively** — precise context, verification steps, no vague handoffs
- **Parallel dispatch** — launch independent agents in a single message
- **Anti-patterns to avoid** — duplicated work, serialized independent tasks, vague instructions

## Install

```bash
npx skills add dcjohnston/async-vibes
```

Or manually copy `SKILL.md` into your project's `.claude/skills/`, `.cursor/skills/`, or equivalent agent skills directory.

## Supported Agents

Any agent that reads SKILL.md files, including:

- Claude Code
- Cursor
- GitHub Copilot
- Cline
- Windsurf
- And [40+ others](https://skills.sh)

## When to Use

This skill is most useful on:

- Multi-system projects (backend + frontend + infra)
- Codebases with defined sub-agents or specialized tools
- Any project where changes regularly span multiple files or components
- Teams that want consistent agent delegation behavior across contributors

## License

MIT
