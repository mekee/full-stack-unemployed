# Claude Code / Agent SDK Integration

## Role

Claude Code is the actual software developer. The orchestrator supervises it and should not duplicate its reasoning, coding or tool-use loop.

## Interfaces

Use Claude Code CLI and VS Code extension for human-facing work. For automated orchestration, prefer the Claude Agent SDK where its capabilities fit the requirement. Keep a thin `ClaudeAdapter` so the orchestrator is not coupled to SDK internals.

## Adapter responsibilities

- create/start a session;
- supply execution context;
- select model/role configuration;
- apply permission policy;
- send human answers;
- receive agent events;
- detect questions/completion/phase changes;
- interrupt;
- resume where supported;
- stop;
- capture session identifiers and diagnostics.

## Session vs execution

An **execution** is an orchestrator-level durable concept. A **Claude session** is an implementation detail. One execution may have multiple Claude sessions due to crash recovery, review isolation or restart of a disposable session.

Therefore the SQLite execution record must never depend on a live Claude process.

## Context injection

Provide Claude with:

1. core operating rules;
2. project rules;
3. phase rules;
4. current Issue and conversation context;
5. Wiki instructions/location;
6. Git/worktree information;
7. handover/recovery context when applicable.

Avoid a giant dynamically generated prompt when native Claude Code configuration/files can carry stable rules.

## Fresh review context

Autonomous review should normally use a fresh Claude context to reduce confirmation bias from the implementation session.

## Interrupts

Use the SDK/session mechanism for controlled interrupts when possible. The orchestrator must still persist the intended state transition before/after an interrupt so a crash during interruption is recoverable.

## Provider/model

Initial model provider: Z AI. Initial model: GLM 5.2. Keep model roles configurable (`fast`, `standard`, `deep`) rather than hard-coding Haiku/Sonnet/Opus semantics.

## Failure handling

The adapter must distinguish at least: normal completion, explicit question/wait, user interrupt, process failure, tool/policy denial and unknown termination.

## Human VS Code usage

The VS Code extension remains a useful manual takeover/inspection interface. The orchestrator should not assume that every Claude session is invisible or exclusively machine-owned.
