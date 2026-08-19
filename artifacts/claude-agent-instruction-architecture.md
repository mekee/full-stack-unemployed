# Claude Agent Instruction Architecture

## Goal

Provide stable, understandable rules without creating one enormous prompt.

## Layers

```text
Core operating rules
      ↓
Project rules
      ↓
Phase rules
      ↓
Task context
      ↓
Current human conversation
```

## Core rules

Define:

- autonomous developer role;
- rigorous intake behavior;
- ambiguity handling;
- human approval boundaries;
- Git/worktree rules;
- Wiki protocol;
- question protocol;
- handover/recovery protocol;
- safety rules;
- completion criteria.

## Project rules

Project-specific conventions belong in project configuration and/or `CLAUDE.md`/Wiki, depending on whether they are executable agent rules or durable knowledge.

## Phase rules

Phase instructions should make the current authority explicit. Intake must not edit source. Planning must not implement. Implementation may edit within the authorized worktree. Review should inspect independently and fix only within policy.

## Task context

The orchestrator supplies Issue number, title, body, labels, relevant comments, plan/approval status, repository/worktree, branch and known dependencies.

## Wiki protocol

Claude should discover relevant Wiki content rather than ingest the whole Vault. Canonical Wiki content is read-only. Proposed additions go only to `/inbox`.

## Completion signaling

Claude should not close the Issue or declare human approval merely because coding is complete. It should report implementation/PR readiness; the orchestrator observes GitHub/PR state.

## Question signaling

When a human decision is genuinely required, Claude should produce a structured question and enter a wait state rather than guessing.

## Avoid prompt inflation

Move stable rules into files/configuration and provide only current execution context dynamically. Keep instructions testable and versioned.
