# Handover and Context Persistence

## Purpose

A handover is a Markdown artifact that allows a fresh Claude session to understand how to resume an execution without depending on the previous context window.

## When to update

Update at meaningful checkpoints:

- intake conclusion;
- major discovery;
- major decision;
- plan approval;
- implementation milestone;
- important human interaction;
- pause/stop;
- failure/recovery;
- before declaring completion.

Do not update on every token or trivial tool call.

## Required content

A handover should include:

- execution/task identity;
- current objective;
- current phase;
- concise understanding of requirements;
- accepted decisions;
- important findings;
- implementation progress;
- files/components affected;
- tests/results;
- current Git/PR state;
- unresolved questions;
- known risks;
- exact next steps for a fresh session.

## Separation of concerns

Claude owns semantic handover content. The orchestrator owns mechanical runtime state in SQLite. Neither should pretend to replace the other.

## Persistence location

Handover artifacts are local runtime artifacts, not project Wiki content. Project knowledge discovered during execution belongs in Wiki `/inbox` when appropriate.

## Recovery principle

A new Claude session must verify the real repository/GitHub state instead of blindly trusting the handover. Handover is a strong context aid, not a proof of current reality.
