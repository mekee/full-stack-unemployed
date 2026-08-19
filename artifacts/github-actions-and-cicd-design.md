# GitHub Actions and CI/CD Design

## Principle

Use GitHub Actions as the only CI/CD system initially. The orchestrator observes and coordinates; it does not become a CI server.

## PR pipeline

```text
Claude push
  -> GitHub PR
  -> Actions: lint/test/build/etc.
  -> checks reported on PR
  -> orchestrator observes results
```

## Responsibilities

### Claude

- run fast local tests where useful;
- fix source/test failures;
- prepare PR.

### GitHub Actions

- authoritative CI execution;
- build artifacts;
- deployment workflows where applicable;
- protected branch checks.

### Orchestrator

- detect check completion/failure;
- route failures to the review/fix loop;
- stop after configured fix-loop limits;
- never bypass branch protection.

## Deployment

Production deployment should remain a GitHub Actions responsibility. The agent should not receive unrestricted production credentials simply because deployment is automated.

## Workflow changes

Changing CI definitions is source-code work and should be reviewed like any other implementation. The agent must not modify workflows merely to make a failing check disappear.

## Future

If GitHub Actions proves insufficient for a particular workload, introduce the smallest additional mechanism that solves the demonstrated problem. Do not pre-build a second automation platform.
