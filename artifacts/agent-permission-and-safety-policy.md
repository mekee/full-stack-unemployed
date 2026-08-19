# Agent Permission and Safety Policy

## Principle

Prompts describe behavior; permission controls enforce authority. Never treat a prompt alone as a security boundary.

## Phase matrix

| Capability | Intake | Planning | Implementation | Review/Fix | Release |
|---|---:|---:|---:|---:|---:|
| Read source | yes | yes | yes | yes | yes |
| Read Wiki | yes | yes | yes | yes | yes |
| Read GitHub | yes | yes | yes | yes | yes |
| Write source | no | no | yes | yes | policy-specific |
| Run tests | optional/read-only | optional | yes | yes | CI preferred |
| Commit | no | no | yes | yes | no need |
| Push | no | no | yes | yes | no need |
| Merge PR | no | no | no | no | human |
| Deploy | no | no | no | no | GitHub Actions policy |

## Dangerous operations

Deny by default or require explicit policy for:

- force push;
- deleting unrelated files/directories;
- destructive database operations;
- modifying production infrastructure;
- reading secret stores not required for the task;
- changing orchestrator security policy;
- modifying CI/CD to bypass checks;
- changing branch protection;
- arbitrary external side effects.

## Defense in depth

1. Claude instruction rules.
2. Claude Code permission mode/allowed tools.
3. Pre-tool hooks/policy enforcement where supported.
4. Worktree isolation.
5. GitHub branch protection and required checks.
6. Human merge boundary.

## Scope rule

Claude should have broad authority inside the isolated task worktree when implementation is authorized, but narrow authority outside it.

## Secret handling

Secrets must come from environment/secret mechanisms and never be written into Issue comments, handover files, SQLite, Wiki or commits.

## Stop behavior

Explicit stop is authoritative. Stop the active execution, record the decision, update handover, and discard uncommitted changes according to the selected policy. A stopped execution cannot be resumed; `/restart` creates a new execution.
