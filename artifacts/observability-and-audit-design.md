# Observability and Audit Design

## Goal

Make the system useful as a learning laboratory by making decisions and failures explainable.

## Required observability

The operator should be able to determine:

- which Issue is executing;
- current execution ID;
- current phase/state;
- Claude session ID;
- worktree and branch;
- last activity/heartbeat;
- open question;
- queued comments;
- last significant event;
- recovery attempts;
- CI/PR status;
- reason for waiting/failure.

## Sources

1. SQLite event history — mechanical/audit detail.
2. Structured local logs — process diagnostics.
3. GitHub comments — human-visible decisions and questions.
4. Handover — semantic context.
5. Git history/PR — implementation evidence.

## CLI status

A simple `status` command should be the first operational interface. A web dashboard is intentionally deferred.

## Event categories

Examples:

- `ISSUE_DISCOVERED`
- `EXECUTION_QUEUED`
- `STATE_CHANGED`
- `CLAUDE_STARTED`
- `CLAUDE_STOPPED`
- `QUESTION_ASKED`
- `ANSWER_COMPLETED`
- `COMMENT_QUEUED`
- `WORKTREE_CREATED`
- `COMMIT_CREATED`
- `PR_OPENED`
- `CI_CHANGED`
- `RECOVERY_STARTED`
- `RECOVERY_COMPLETED`
- `EXECUTION_FAILED`
- `EXECUTION_COMPLETED`

## Learning feedback

Periodically inspect execution history for recurring failure patterns. Convert useful discoveries into GitHub Issues and, when stable and project-specific, into Wiki `/inbox` proposals.
