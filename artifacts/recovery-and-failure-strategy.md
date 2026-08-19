# Recovery and Failure Strategy

## Principle

Claude sessions are disposable; executions are durable. Recovery should reconstruct enough context to continue safely rather than preserving a fragile process indefinitely.

## Failure classes

1. Claude process/session crash.
2. Orchestrator crash/restart.
3. Git/worktree inconsistency.
4. GitHub/API transient failure.
5. CI failure.
6. Agent semantic failure.
7. Policy/permission denial.
8. Terminal user stop/cancellation.

## Recovery flow

```text
unexpected failure
  -> persist failure event
  -> preserve/update recovery artifact
  -> verify SQLite state
  -> verify Git/worktree
  -> verify GitHub Issue/PR
  -> load latest handover
  -> start fresh Claude session
  -> ask Claude to verify reality
  -> continue or escalate
```

## Retry policy

Use bounded retries for infrastructure failures. Do not endlessly retry semantic failures. Recovery attempts are recorded in SQLite. The initial implementation can use a conservative configurable maximum, e.g. three attempts, then enter `FAILED` and require `/restart`.

## Checkpoints

A checkpoint is a semantic declaration by Claude that the handover reflects a safe continuation point. A crash after a checkpoint should prefer resuming from that context, but the new session must still verify actual state.

## Stop vs pause

- **Pause:** resumable; preserve current state and worktree.
- **Stop:** terminal for that execution; document decision, discard uncommitted changes according to policy, preserve committed history, and require `/restart` for a new execution.

## Restart

Restart creates a new execution record. It inherits the latest task context/handover and references the prior execution. This preserves auditability and makes stale-session analysis easier.

## Stale sessions

SQLite should expose executions that have not emitted a heartbeat/event for a configurable period. Stale detection should be conservative: verify the process/session and Git state before marking an execution failed.
