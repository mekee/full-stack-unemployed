# Human-Agent Conversation Protocol

## Goals

Make GitHub comments a reliable conversational interface without allowing asynchronous comments to unpredictably disrupt autonomous work.

## Intake mode

All new comments after an agent question are conversational inputs. Multiple comments can form a single answer. The orchestrator collects them until a configurable completion keyword is received; initial default: `DONE`.

Example:

```text
Agent: Should we use A or B?
Human: I prefer A.
Human: One more constraint: it must work offline.
Human: DONE
```

The three comments become one answer payload.

## Non-intake modes

New comments are queued. They are not injected into the active Claude context immediately. At a safe boundary, the orchestrator presents the accumulated comments as one or more queued human inputs.

## Commands

Commands such as `/pause`, `/resume`, `/stop`, `/restart` and `/status` are deterministic orchestrator actions. Commands should be recognized only where policy allows them.

## Questions

A question comment should contain:

1. what Claude needs to know;
2. relevant facts discovered;
3. important constraints;
4. options when applicable;
5. recommendation when appropriate;
6. explicit decision requested.

## Approval

For planned tasks, human approval must be explicit. Silence, an unrelated comment or a generic acknowledgement must not be interpreted as approval.

## Queue semantics

Each comment should be persisted with its GitHub comment ID before being considered processed. Re-polling must not duplicate a comment. The answer batch should retain original order and author metadata.

## Interruptions

Human commands have priority over ordinary comments. For example, `/stop` must not wait for Claude to reach a normal conversational boundary if a safe interrupt is available.
