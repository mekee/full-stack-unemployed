# MVP Implementation Roadmap

## Guiding rule

Build the smallest useful loop first, then use the system to learn what deserves complexity.

## MVP 1 — Autonomous development loop

Goal: prove the basic mechanical flow.

```text
ai-ready Issue -> FIFO -> worktree -> Claude -> commits -> PR -> CI -> human merge -> Issue resolution
```

Implement:

- GitHub polling;
- one execution slot;
- worktree creation;
- Claude adapter;
- PR detection;
- completion detection;
- basic logs.

Do not implement sophisticated intake or recovery yet.

## MVP 2 — Persistent orchestration

Add:

- SQLite;
- execution state machine;
- event history;
- idempotent polling;
- waiting state;
- comment persistence.

## MVP 3 — Conversational intake

Add:

- rigorous intake instructions;
- questions;
- multi-comment answer batching;
- `DONE` completion keyword;
- explicit commands;
- scope triage;
- plan + approval;
- Issue decomposition.

## MVP 4 — Recovery

Add:

- handover;
- recovery artifact;
- crash/stale detection;
- bounded recovery attempts;
- pause/stop/restart;
- new execution lineage.

## MVP 5 — Review automation

Add:

- independent AI review session;
- CI-aware fix loop;
- review findings;
- bounded fix cycles;
- human merge boundary.

## MVP 6 — Hardening

Add:

- phase permissions;
- hooks/policy enforcement;
- better observability;
- configuration validation;
- `doctor` diagnostics;
- failure injection tests.

## MVP 7 — Dogfooding

Use the orchestrator to implement improvements to the orchestrator itself. Every discovered weakness becomes a real Issue and is handled through the workflow.
