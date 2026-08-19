# Orchestrator Configuration Schema

## Principle

Configuration should be small, readable, versionable and separate from runtime SQLite state. Secrets must be supplied externally.

## Example

```yaml
project:
  name: example
  repository: owner/repository

github:
  pollingIntervalSeconds: 30
  aiReadyLabel: ai-ready
  answerCompletionKeyword: DONE

execution:
  maxConcurrent: 1
  queue: fifo

models:
  fast: glm-5.2
  standard: glm-5.2
  deep: glm-5.2

wiki:
  path: ~/projects/example/llm-wiki

recovery:
  maxAttempts: 3
  staleAfterSeconds: 300

commands:
  pause: /pause
  resume: /resume
  stop: /stop
  restart: /restart
  status: /status
```

## Permission configuration

Permission policy should be configurable by phase, but dangerous defaults remain restrictive. Configuration should not be able to silently bypass GitHub branch protection or external platform policy.

## Model configuration

Model names are configuration, not code-level concepts. Use role names (`fast`, `standard`, `deep`) so provider/model experiments do not change orchestrator logic.

## Project configuration vs Wiki

Configuration contains executable behavior and infrastructure locations. The Wiki contains project knowledge. Do not use either as a dumping ground for the other.

## Secrets

API keys, GitHub tokens and provider credentials are environment/OS secret values. Never commit them to the repository or store them in the config file.
