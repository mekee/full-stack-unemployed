# MVP Acceptance Criteria

## MVP 1

- An open Issue labeled `ai-ready` is discovered.
- FIFO ordering is deterministic.
- No more than one autonomous execution runs.
- A dedicated branch/worktree is created.
- Claude receives the task context.
- Claude can implement and test within policy.
- A PR is created/updated.
- GitHub Actions can run normally.
- Human merge is detected.
- The Issue is resolved and the worktree is cleaned.
- Repeated polling does not create duplicate executions.

## MVP 2

- Restarting the orchestrator preserves execution state.
- Every significant lifecycle transition is stored in SQLite.
- GitHub comments are not processed twice.
- A waiting execution remains waiting across process restart.
- SQLite and GitHub divergence can be diagnosed.

## MVP 3

- Intake refuses to implement when requirements are materially ambiguous.
- Multiple human comments can be combined into one answer using `DONE`.
- Non-intake comments are queued.
- Small clear tasks execute without unnecessary approval ceremony.
- Normal tasks produce a plan and wait for explicit approval.
- Broad tasks are decomposed into child Issues.

## MVP 4

- Claude/session crashes can be recovered without losing execution identity.
- A fresh session can use handover and verify actual state.
- Pause is resumable.
- Stop is terminal for that execution.
- Restart creates a new execution lineage.
- Stale sessions are detectable and diagnosable.

## MVP 5

- Review runs independently from implementation context.
- Review findings can trigger autonomous fixes.
- CI results participate in the fix loop.
- The loop is bounded and cannot run forever.
- Human merge remains the resolution boundary.

## MVP 6

- Phase permissions are enforced mechanically.
- Dangerous operations are denied by default.
- Audit logs explain significant actions.
- Configuration validation catches unsafe/invalid settings.
- Failure injection tests demonstrate recovery behavior.
