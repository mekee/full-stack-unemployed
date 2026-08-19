# Orchestrator Repository Structure

## Suggested structure

```text
ai-dev-orchestrator/
├── src/
│   ├── github/
│   ├── scheduler/
│   ├── execution/
│   ├── claude/
│   ├── git/
│   ├── wiki/
│   ├── conversation/
│   ├── db/
│   ├── policy/
│   └── config/
├── migrations/
├── tests/
├── config/
├── scripts/
├── README.md
├── package.json
└── tsconfig.json
```

## Module responsibilities

- `github/` — GitHub API/CLI adapter and idempotent mutations.
- `scheduler/` — FIFO candidate discovery and single-slot execution.
- `execution/` — state machine and lifecycle orchestration.
- `claude/` — Agent SDK adapter, sessions and context injection.
- `git/` — branches, worktrees and repository verification.
- `wiki/` — Wiki discovery/path validation.
- `conversation/` — questions, answer batches and commands.
- `policy/` — phase permissions and safety checks.
- `db/` — SQLite schema and repositories.
- `config/` — configuration loading/validation.

## Architectural boundary

Modules communicate through explicit application services/events rather than reaching directly into one another's databases or GitHub calls. Keep the abstraction lightweight; the goal is isolation of external dependencies, not a large framework.

## CLI

Start with a small CLI:

- `status`
- `run`
- `doctor`
- `config validate`
- `execution inspect <id>`

A daemon/service mode can be added later.

## Testing

Unit-test state transitions, comment batching, scheduling and policy decisions. Integration-test GitHub/Claude/Git worktree adapters with controlled fixtures. Keep the state machine testable without external services.
