# Git and Worktree Lifecycle

## Goal

Keep autonomous changes isolated from the human's normal working tree while preserving a simple one-task/one-branch/one-PR model.

## Lifecycle

```text
eligible Issue
  -> create feature branch
  -> create isolated worktree
  -> Claude edits/tests
  -> commits
  -> push
  -> open/update PR
  -> CI/review/fix loop
  -> human merge
  -> cleanup worktree
```

## Branch naming

Use a deterministic convention such as `ai/issue-<number>-<slug>`. The issue number must be included so the branch can be reconciled after restart.

## Worktree metadata

Store path, branch, base ref, execution ID and creation time in SQLite. Never assume a worktree exists merely because the database says it does; verify the filesystem and Git state during recovery.

## Commit policy

Claude may commit during implementation. Commits should be meaningful and scoped. Avoid requiring a particular number of commits. The PR is the review unit.

## Push policy

Push is allowed only during authorized implementation/review phases. Force-push should be denied by default.

## Cleanup

After PR merge and successful Issue resolution:

1. verify PR merge;
2. record completion;
3. remove worktree;
4. optionally delete local branch;
5. preserve remote branch according to repository policy.

For stop/failure, preserve useful committed work but remove uncommitted changes only when the stop policy explicitly requires it. The current chosen stop policy is to discard uncommitted changes and document the decision in handover.

## Recovery

A new Claude session must inspect actual Git status, branch, commits, PR and worktree state before continuing. Database state alone is never sufficient to assume the repository is healthy.
