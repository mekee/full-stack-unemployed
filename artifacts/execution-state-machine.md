# Execution State Machine

## States

- `DISCOVERED` — Issue detected as eligible.
- `QUEUED` — waiting for the single execution slot.
- `INTAKE` — rigorous requirements discovery.
- `WAITING_FOR_HUMAN` — explicit question or approval required.
- `TRIAGE` — determine scope/action path.
- `PLANNING` — implementation plan being constructed.
- `DECOMPOSING` — broad task being split into child Issues.
- `READY_TO_EXECUTE` — scope is approved/clear.
- `IMPLEMENTING` — coding/testing.
- `REVIEWING` — autonomous review.
- `FIXING` — autonomous fixes after review/CI.
- `PAUSED` — deliberately paused and resumable.
- `RECOVERING` — process/session failure recovery.
- `STOPPED` — intentionally terminated; not resumable.
- `FAILED` — terminal failure; restart creates a new execution.
- `CANCELLED` — explicitly cancelled.
- `COMPLETED` — PR merged and task resolved.

## Important transitions

```text
DISCOVERED -> QUEUED -> INTAKE
INTAKE -> WAITING_FOR_HUMAN
INTAKE -> TRIAGE
TRIAGE -> DECOMPOSING
TRIAGE -> READY_TO_EXECUTE
READY_TO_EXECUTE -> IMPLEMENTING
IMPLEMENTING -> REVIEWING
REVIEWING -> FIXING
FIXING -> REVIEWING
REVIEWING -> WAITING_FOR_HUMAN
WAITING_FOR_HUMAN -> previous safe phase
IMPLEMENTING/REVIEWING/FIXING -> PAUSED
PAUSED -> previous safe phase
RUNNING phases -> RECOVERING on unexpected failure
RECOVERING -> previous safe phase
RUNNING phases -> STOPPED on explicit stop
STOPPED -> new execution only
FAILED -> new execution only
PR MERGED -> COMPLETED
```

## Special rules

- A paused execution may resume.
- A stopped execution may not resume; `/restart` creates a new execution.
- A completed execution cannot be restarted through normal workflow.
- Human approval is represented by an explicit Issue comment/action and is not inferred from silence.
- The parent task is resolved after all required children are completed.
- One active execution globally is sufficient for the initial system.

## State-machine discipline

Every transition should be explicit, persisted in SQLite, logged as an event, and ideally accompanied by a human-visible GitHub comment only when the transition materially affects the user. Polling must be idempotent.
