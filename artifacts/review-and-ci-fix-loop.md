# Autonomous Review and CI Fix Loop

## Objective

Allow autonomous quality improvement after implementation while preserving a human merge boundary.

## Flow

```text
implementation
  -> PR
  -> CI
  -> independent AI review
  -> findings?
      yes -> fix -> CI/review again
      no  -> human review/merge
```

## Independent review

Prefer a fresh Claude session for review. Give it the PR diff, Issue requirements, approved plan, relevant Wiki context and CI results. Do not give it unnecessary implementation-session history.

## Findings

Findings should be classified as:

- blocking correctness issue;
- test failure;
- requirement mismatch;
- maintainability issue;
- optional suggestion.

Only actionable/blocking findings should automatically trigger a fix loop.

## Bounded loop

The orchestrator must enforce a maximum number of autonomous review/fix cycles per execution. After the limit, transition to `WAITING_FOR_HUMAN` or `FAILED` according to policy rather than looping indefinitely.

## Human boundary

Human review and PR merge remain mandatory. A passing CI run or AI review is not equivalent to human approval.

## CI authority

GitHub Actions remains the CI/CD system. The orchestrator observes checks and routes results to Claude. It should not reimplement CI execution locally except for fast developer feedback.

## Review comments

Machine review findings should be distinguishable from human review comments. New human comments during review are queued under the normal non-intake comment policy unless they are explicit commands.
