# Automated AI Software Developer — Artifact Index

This directory contains the architectural, technical, and implementation decisions for the automated AI software-development agent. The documents are intentionally separated so a future agent session can recover the design from this index without relying on conversational context.

## Start here

| Document | Description |
|---|---|
| [Automated AI Software Developer — Detailed Architecture Plan](./automated-ai-software-developer-detailed-architecture-plan.md) | High-level architecture, principles, lifecycle, major boundaries and incremental strategy. **Start here.** |
| [Technical Specification v1](./technical-specification-v1.md) | Concrete component responsibilities, interfaces, invariants, idempotency and external integration rules. |
| [Development Cookbook](./development-cookbook.md) | Human-oriented step-by-step implementation guide from repository bootstrap through the autonomous development loop, recovery, review and dogfooding. |
| [AI Agent Development Cookbook](./ai-agent-development-cookbook.md) | **Operating manual for Claude Code.** Behavioral rules, decision-making, ambiguity escalation, human interaction, implementation workflow, recovery, safety and autonomous-development procedures. **Hand this to Claude Code.** |

## Task intake and workflow

| Document | Description |
|---|---|
| [Intake and Scope Triage Protocol](./intake-and-scope-triage-protocol.md) | Rigorous intake, ambiguity handling and the three-way scope triage: execute, plan/approve, or decompose. |
| [GitHub Workflow and Conventions](./github-workflow-and-conventions.md) | GitHub Issues, labels, commands, comments, parent/child tasks, PR merge and resolution conventions. |
| [Human-Agent Conversation Protocol](./human-agent-conversation-protocol.md) | Multi-comment answers, `DONE`, queued comments, questions, approvals and explicit commands. |
| [Execution State Machine](./execution-state-machine.md) | Runtime states and allowed transitions, including waiting, pause, stop, recovery and completion. |

## Claude Code and agent control

| Document | Description |
|---|---|
| [Claude Code / Agent SDK Integration](./claude-code-agent-sdk-integration.md) | Boundary between orchestrator and Claude Code, session handling, automation and model/provider configuration. |
| [Claude Agent Instruction Architecture](./claude-agent-instruction-architecture.md) | Layered instruction strategy for core rules, project rules, phases and task context. |
| [Agent Permission and Safety Policy](./agent-permission-and-safety-policy.md) | Phase-specific authority, dangerous-operation controls and defense-in-depth safety model. |
| [Autonomous Review and CI Fix Loop](./review-and-ci-fix-loop.md) | Independent AI review, CI-driven fixes, bounded loops and human merge boundary. |

## Persistence and recovery

| Document | Description |
|---|---|
| [SQLite Runtime State Schema](./sqlite-runtime-state-schema.md) | Proposed runtime tables, event history, questions, comment batches, worktrees and recovery records. |
| [Handover and Context Persistence](./handover-and-context-persistence.md) | Semantic handover requirements and separation from mechanical orchestrator state. |
| [Recovery and Failure Strategy](./recovery-and-failure-strategy.md) | Crash handling, stale sessions, retry limits, pause/stop/restart and execution lineage. |
| [Observability and Audit Design](./observability-and-audit-design.md) | Runtime visibility, event taxonomy, CLI status and learning/diagnostic feedback. |

## Repository and platform integration

| Document | Description |
|---|---|
| [Git and Worktree Lifecycle](./git-and-worktree-lifecycle.md) | Branch/worktree isolation, commits, push, PR lifecycle, cleanup and recovery verification. |
| [LLM Wiki Integration Protocol](./llm-wiki-integration-protocol.md) | Wiki Vault authority, retrieval, read-only canonical knowledge and `/inbox` proposals. |
| [GitHub Actions and CI/CD Design](./github-actions-and-cicd-design.md) | GitHub-native CI/CD responsibilities and the orchestrator's role in observing/fixing failures. |
| [Orchestrator Configuration Schema](./orchestrator-configuration-schema.md) | Initial YAML-style configuration model, model roles, polling, recovery and command configuration. |
| [Orchestrator Repository Structure](./orchestrator-repository-structure.md) | Proposed TypeScript/Node.js repository structure and module boundaries. |

## Implementation

| Document | Description |
|---|---|
| [MVP Implementation Roadmap](./mvp-implementation-roadmap.md) | Incremental implementation from the basic Issue-to-PR loop through recovery, review and hardening. |
| [MVP Acceptance Criteria](./mvp-acceptance-criteria.md) | Observable acceptance criteria for each MVP stage. |

## Decision baseline

The current baseline is intentionally simple: TypeScript/Node.js, SQLite, Git/GitHub, GitHub Actions, Claude Code/Agent SDK, a local Markdown LLM Wiki Vault, and Z AI / GLM 5.2 initially. One human and one concurrent autonomous execution are assumed. FIFO is used instead of a separate priority engine.

These documents are a **design baseline**, not immutable law. Future implementation findings should create explicit GitHub Issues and update the relevant document when a design decision changes. The index is the recovery entry point: a fresh agent can read it first, then follow only the documents relevant to the current problem.
