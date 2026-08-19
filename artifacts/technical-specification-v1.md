# Automated AI Software Developer — Technical Specification v1

## Purpose

Turn the architecture into an implementable specification without prematurely fixing low-level details that can be derived during implementation.

## Components

1. **Orchestrator** — TypeScript/Node.js local process.
2. **GitHub adapter** — reads Issues/Comments/PR/Actions state and performs approved mutations.
3. **Scheduler** — FIFO, maximum one active execution.
4. **Execution manager** — owns lifecycle and Claude sessions.
5. **SQLite repository** — persistent runtime state and event history.
6. **Claude adapter** — isolates Claude Agent SDK/CLI specifics.
7. **Git/worktree manager** — branch/worktree lifecycle.
8. **Conversation manager** — questions, answer batches and commands.
9. **Policy engine** — phase-specific permissions and safety rules.
10. **Wiki adapter** — supplies Wiki location/context and enforces read/write convention.
11. **Recovery manager** — handover, crash detection and restart.

## Core invariants

- At most one autonomous execution is active.
- Every autonomous task has a GitHub Issue.
- Every implementation execution uses an isolated worktree.
- Every implementation task normally produces one PR.
- Human merge is the resolution boundary.
- SQLite is authoritative for mechanical execution state.
- GitHub is authoritative for human workflow/authorization signals.
- Claude is authoritative for semantic development work, not orchestrator lifecycle.
- No implementation starts before intake establishes a sufficiently clear task.

## External interfaces

### GitHub

Read: Issues, labels, comments, project/milestone metadata as needed, PRs, checks/workflow results.

Write: comments, labels/status markers, branches/PR metadata where required, issue closure after verified resolution.

### Claude

The adapter should expose a small internal interface such as:

- `startExecution(context)`
- `sendInput(executionId, input)`
- `interrupt(executionId)`
- `resume(executionId, sessionId)`
- `stop(executionId)`
- `getStatus(executionId)`
- `collectEvents(executionId)`

The concrete implementation may use the Agent SDK rather than shelling out to the CLI for automation.

### Git

- create branch
- create worktree
- inspect status
- commit
- push
- cleanup worktree

## Polling/event model

The orchestrator periodically polls relevant GitHub resources and translates observations into internal events. It must persist event identifiers or equivalent cursors so repeated polling is idempotent.

## Idempotency

Every external mutation should be safe to retry or detect as already completed. The orchestrator must not create duplicate executions merely because a polling cycle is repeated.

## Configuration

Configuration should be human-readable and versionable. Secrets must not be committed. Runtime state belongs in SQLite. Model/provider selection must be configurable.

## Non-goals for v1

No multi-user coordination, distributed workers, web dashboard, external queue, cloud database, custom vector store or custom coding-agent runtime.
