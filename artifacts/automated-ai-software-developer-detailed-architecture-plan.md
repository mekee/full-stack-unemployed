# Automated AI Software Developer — Detailed Architecture Plan

## 1. Executive architecture

The system should be deliberately small. GitHub remains the human-visible development workflow; a local TypeScript/Node.js orchestrator manages lifecycle and runtime state; Claude Code is the actual AI developer; the LLM Wiki Vault is the durable project-knowledge layer; GitHub Actions provides CI/CD.

```text
Human (GitHub / VS Code)
          |
          v
   Local Orchestrator
   - GitHub polling
   - SQLite runtime state
   - FIFO scheduler
   - task lifecycle
   - conversation routing
   - recovery
   - policy enforcement
          |
          v
   Claude Code / Agent SDK
   - investigation
   - planning
   - coding
   - testing
   - review/fix
          |
     +----+----+
     |         |
     v         v
 Git worktree  LLM Wiki Vault
     |
     v
 GitHub PR -> GitHub Actions -> human merge -> Issue resolved
```

## 2. Core principles

1. Build the simplest reliable and robust solution possible.
2. Use the project as a learning laboratory for agentic software development.
3. Minimize ecosystems and technologies.
4. Prefer GitHub-native functionality for tickets, collaboration, versioning, PRs and CI/CD.
5. Use Claude Code CLI/VS Code for human interaction and Claude Agent SDK for automation where appropriate.
6. Use Z AI / GLM 5.2 initially, but keep provider/model selection replaceable.
7. Optimize for one human and one autonomous execution at a time; do not prematurely solve multi-user concurrency.
8. Use the LLM Wiki Vault as primary durable project-specific knowledge.
9. Use SQLite for orchestrator/runtime state, not as a second ticketing or knowledge system.
10. Keep the orchestrator a supervisor/coordinator, not a second coding agent.

## 3. Sources of truth

- **GitHub:** human-visible requirements, task conversation, approvals, PR/review lifecycle.
- **Git:** implementation truth: source, commits, branches and worktrees.
- **LLM Wiki Vault:** durable project knowledge and architecture/domain context.
- **SQLite:** mechanical runtime state and audit/event history.

Suggested authority order when sources disagree: explicit current human decision > current Issue requirements/approved plan > actual repository state > Wiki > agent assumptions.

## 4. Task lifecycle

```text
AI-ready Issue
   -> FIFO scheduling
   -> Intake
   -> Scope triage
      -> small/clear: execute
      -> normal: plan -> human approval -> execute
      -> too broad: decompose into child Issues
   -> implementation
   -> PR / CI
   -> autonomous review/fix loop
   -> human PR review/merge
   -> Issue resolved
```

Exceptional states include WAITING_FOR_HUMAN, PAUSED, STOPPED, FAILED, CANCELLED and RECOVERING.

## 5. Intake philosophy

Intake is deliberately rigorous. Claude should grill the human until it has a clear perspective on what needs to be done. It should identify ambiguity, missing requirements, hidden assumptions, dependencies, acceptance criteria and scope problems before solutioning begins.

The agent must not start implementing merely because an Issue contains a plausible request.

## 6. Scope triage

Three outcomes are required:

- **Small and unambiguous:** planning is a formality; immediate execution is expected.
- **Appropriate scope but meaningful complexity:** produce and document an implementation plan as an Issue comment; wait for human approval.
- **Too broad:** decompose into smaller GitHub Issues/sub-issues before implementation.

## 7. Human conversation

During intake, all new comments are conversational inputs. Multiple comments can form one answer. A dedicated configurable completion keyword such as `DONE` closes the answer batch.

During other phases, new human comments are queued until the execution reaches a safe conversational boundary. Explicit control commands such as `/pause`, `/resume`, `/stop` and `/restart` should be deterministic orchestrator commands rather than natural-language instructions.

## 8. Git strategy

One task should normally map to one feature branch/worktree and one PR. The normal developer worktree should not be modified by autonomous execution. The orchestrator creates and cleans isolated worktrees.

## 9. Permissions

Permissions are phase-specific. Intake and planning should be read-only against source. Implementation may edit, test, commit and push within the task worktree. Destructive or consequential operations must be explicitly authorized. Security must not rely solely on prompts; Claude Code permission controls and hooks should enforce policy.

## 10. Review and resolution

Implementation produces a PR. GitHub Actions supplies CI. Autonomous review/fix loops may continue without human approval, but human approval is required at the merge boundary. PR merge is the simple, explicit signal that the task is resolved.

## 11. Recovery and handover

Claude maintains semantic handover information; the orchestrator maintains mechanical runtime/recovery state in SQLite and recovery artifacts. Claude sessions are disposable. Execution state must survive process/session failure.

A stopped execution is not resumable; a paused execution is resumable. Restarting a stopped/failed execution creates a new execution that inherits the latest handover/context.

## 12. Recommended initial stack

- TypeScript
- Node.js
- SQLite
- Claude Agent SDK / Claude Code
- Git / GitHub CLI/API
- GitHub Actions
- Markdown LLM Wiki Vault
- Z AI / GLM 5.2 initially

Avoid Kubernetes, Redis, queues, microservices, custom CI, custom ticketing, custom vector databases, MCP for the Wiki, dashboards and multi-agent swarms until a demonstrated need exists.

## 13. Incremental implementation

Build in stages rather than all at once:

1. Issue -> worktree -> Claude -> PR -> merge -> resolution.
2. Add SQLite and polling.
3. Add intake, questions and multi-comment answers.
4. Add scope triage, decomposition and plan approval.
5. Add handover/recovery.
6. Add autonomous review/fix loop.
7. Harden permissions, observability and configuration.

## 14. Learning objective

The system should expose enough state and history to answer why the agent acted, what it knew, what it asked, what the human answered, why it waited, how it recovered and what failed. This observability is a first-class requirement because learning about agentic automation is itself a project goal.
