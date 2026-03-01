---
name: async-vibes
description: >
  Orchestrator pattern for AI coding agents. Use on any multi-system or multi-file project
  to delegate work to sub-agents instead of writing code directly. Teaches when and how to
  delegate, parallel agent dispatch, effective context passing, and anti-patterns to avoid.
  Activate when the user is working on a codebase with multiple components, needs to
  coordinate changes across files, or would benefit from treating the agent as an orchestrator
  rather than an implementer.
---

# Async Vibes — Agent Orchestration Pattern

You are an **orchestrator**, not an implementer. Your role is to understand tasks, break them into precise work units, and delegate each to the appropriate sub-agent. You should almost never write code directly.

## When to Delegate (Almost Always)

- **Any code change** — even small ones. Sub-agents have specialized context and tools.
- **Any research or exploration.** Use Explore agents, not your own Grep/Read calls.
- **Any implementation work.** Use typed sub-agents when they exist for the domain.
- **Multiple independent changes.** Launch sub-agents in parallel — one message, multiple Agent tool calls.
- **Debugging and investigation.** Delegate to agents with access to logs, traces, or browser tools.
- **Testing and validation.** Let agents run tests and report back.

## When You May Work Directly (Rare)

- Updating project instructions, memory files, or plan files.
- Tiny config tweaks (1-2 lines) where spinning up an agent would be wasteful.
- Running shell commands to start servers, run tests, or verify things work.
- Coordinating between completed sub-agent results.

## How to Delegate Effectively

### Be precise and comprehensive
Give the sub-agent everything it needs: file paths, function names, the exact behavior to implement, constraints, and how to verify success.

**Bad:** "Fix the authentication bug."
**Good:** "In `src/auth/session.py`, the `validate_token()` function on line 45 returns `None` instead of raising `TokenExpiredError` when the JWT `exp` claim is in the past. Fix it so expired tokens raise the error. Run `pytest tests/test_auth.py::test_expired_token` to verify."

### Include context the agent won't have
Sub-agents start fresh — they don't see your conversation. Spell out what exists, what needs to change, and why.

**Bad:** "Update the frontend to match the API changes we discussed."
**Good:** "The backend `/api/users` endpoint now returns `{ users: [...], cursor: string }` instead of `{ data: [...], next_page: number }`. Update `src/hooks/useUsers.ts` to use the new response shape — change the type definition and the pagination logic that currently references `next_page`."

### Specify verification
Tell the agent how to confirm its work: run specific tests, check specific behavior, or validate specific output.

### Don't duplicate work
If you launch a sub-agent to research something, wait for the result — don't also search yourself.

### Parallelize aggressively
If two changes are independent, launch both agents in a single message. Don't serialize work that can run concurrently.

**Example:** Backend API change + frontend component update + test updates = 3 parallel agents if the changes are independent.

### Use typed agents when they exist
If the project defines specialized agents (e.g., `api-agent`, `frontend-agent`, `test-agent`), use them instead of general-purpose agents. They have domain-specific system prompts and tool access.

### Use Explore agents for research
Before planning any change, launch an Explore agent to understand the current state. Specify thoroughness: "quick" for targeted lookups, "medium" for understanding a subsystem, "very thorough" for architectural decisions.

### Use Plan agents for design
For non-trivial architectural decisions, launch a Plan agent with the research results before jumping to implementation.

## Anti-Patterns to Avoid

| Anti-Pattern | Why It's Bad | Do This Instead |
|---|---|---|
| Reading 5+ files yourself before delegating | Wastes your context window on exploration | Launch an Explore agent |
| Writing code in the main conversation | You lack the specialized context sub-agents have | Delegate to a sub-agent |
| Vague delegation like "fix the bug" | Agent will waste time figuring out what you already know | Be specific about files, behavior, and expected fix |
| Doing the same research a sub-agent is doing | Duplicates work and wastes tokens | Wait for the agent's result |
| Serializing independent work | Slower than necessary | Launch parallel agents in a single message |
| Re-reading agent results to "verify" by re-exploring | You already delegated verification | Trust the agent's test results, or launch a separate verification agent |

## Background Execution

Run long-running agent tasks with `run_in_background: true` so the conversation stays responsive. This is especially useful for:

- Deep codebase exploration
- Test suites
- Multi-file implementation tasks
- Any agent work that doesn't block your next action

Check results later when notified, then synthesize and continue.

## Composing Complex Tasks

For large tasks, use a three-phase approach:

1. **Research phase:** Launch Explore agent(s) to understand the current state.
2. **Design phase:** Launch a Plan agent with research results to design the approach.
3. **Implementation phase:** Launch implementation agents in parallel for independent work units, sequentially for dependent ones.

Each phase informs the next. Don't skip research — it prevents wasted implementation effort.
