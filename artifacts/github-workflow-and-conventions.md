# GitHub Workflow and Conventions

## Purpose

GitHub is the human-visible task and collaboration system. Do not introduce a parallel ticketing system.

## Native objects

- **Issue:** task, requirements, questions and decisions.
- **Project:** organization and workflow views.
- **Milestone:** release/goal grouping.
- **Label:** persistent classification and authorization signals.
- **Sub-issue/dependency:** decomposition and sequencing.
- **PR:** implementation and review boundary.
- **Actions:** CI/CD.

## `ai-ready`

`ai-ready` is the initial authorization label for autonomous intake. The orchestrator discovers eligible open Issues carrying this label and excludes blocked/already-running items.

Scheduling is FIFO. No separate priority field is required initially.

## Commands

Use explicit commands for control actions. Suggested vocabulary:

- `/pause`
- `/resume`
- `/stop`
- `/restart`
- `/status`

The exact syntax should be configurable. Commands are parsed by the orchestrator and must not rely on Claude interpreting intent.

## Intake comments

Claude may ask multiple questions. Human responses can span multiple comments. A configurable completion keyword, initially `DONE`, closes an answer batch. During intake, comments are conversational inputs.

Outside intake, new comments are queued until a safe conversational boundary.

## Planning comments

For non-trivial but appropriately scoped work, Claude posts an implementation plan and waits for explicit human approval. Approval must be represented by a deterministic signal agreed by the workflow, not inferred from a casual comment.

## Issue closure

A PR merge is the simple resolution signal. The orchestrator verifies the PR is merged, records completion, cleans the worktree and closes the Issue if it remains open.

## Parent Issues

When intake identifies excessive scope, Claude decomposes the work into child Issues/sub-issues. The parent remains open until required children are completed, then can be closed.

## Comment style

Agent comments should be concise but structured. Questions should state context, known facts, decision needed and recommendation. State-transition comments should be emitted only when useful to the human.
