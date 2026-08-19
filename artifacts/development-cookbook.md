# Automated AI Software Developer — Development Cookbook

## Purpose

This cookbook is the practical implementation guide for building the automated AI software-development agent described by the architecture and technical specification documents in this directory.

It is intentionally procedural: use it to move from an empty `orchestrator/` directory to a working, self-hosted autonomous development loop. The architecture documents define **what the system should be**; this cookbook explains **how to build it incrementally**.

## 1. Read the design baseline first

Before implementing code, read the following in order:

1. `automated-ai-software-developer-detailed-architecture-plan.md`
2. `technical-specification-v1.md`
3. `execution-state-machine.md`
4. `github-workflow-and-conventions.md`
5. `claude-code-agent-sdk-integration.md`
6. `sqlite-runtime-state-schema.md`
7. `git-and-worktree-lifecycle.md`
8. `agent-permission-and-safety-policy.md`
9. `mvp-implementation-roadmap.md`
10. `mvp-acceptance-criteria.md`

If implementation findings contradict a document, do not silently improvise. Record the discrepancy, resolve it deliberately, and update the relevant artifact.

## 2. Bootstrap the repository

Work in:

```text
full-stack-unemployed/
├── artifacts/
└── orchestrator/
```

Do not move the architecture documents into the application source tree. They are project-level design artifacts.

Create a TypeScript/Node.js application in `orchestrator/`.

Initial technology choices:

- Node.js
- TypeScript
- SQLite
- Claude Agent SDK
- Git/GitHub CLI or GitHub API where necessary
- YAML/JSON configuration
- plain Markdown files for handover and Wiki interaction

Avoid introducing additional infrastructure unless an actual requirement demonstrates the need.

## 3. Establish the minimum development environment

Verify before implementing orchestration:

```bash
node --version
npm --version
git --version
gh --version
claude --version
```

Verify authentication independently:

```bash
gh auth status
```

Verify that Claude Code can run interactively in a test repository before integrating it programmatically.

Configure Z AI / GLM 5.2 through the supported Claude-compatible mechanism. Keep model selection configurable; GLM 5.2 is the initial provider/model choice, not a hard architectural dependency.

## 4. Create the smallest runnable orchestrator

First goal:

```text
orchestrator start
    ↓
loads configuration
    ↓
opens SQLite
    ↓
connects to GitHub
    ↓
prints current state
    ↓
exits cleanly
```

Do not implement the autonomous agent yet.

The first milestone proves configuration, logging, database initialization and GitHub connectivity.

## 5. Implement SQLite before complex orchestration

Create the database according to `sqlite-runtime-state-schema.md`.

At minimum, persist:

- executions
- sessions
- events
- questions
- comment batches
- commands
- worktrees
- recovery attempts

Use migrations from the beginning.

Important invariant:

> SQLite stores orchestrator/runtime state, not project requirements or canonical project knowledge.

Every important state transition should be represented by an event or an auditable record.

## 6. Implement the execution state machine

Implement the state machine as a deterministic module before connecting it to Claude.

It must reject invalid transitions.

The state machine should distinguish at least:

```text
DISCOVERED
INTAKE
WAITING_FOR_HUMAN
PLANNING
AWAITING_APPROVAL
IMPLEMENTING
REVIEWING
CI_FIXING
PAUSED
STOPPED
FAILED
COMPLETED
```

Use the exact canonical state names from `execution-state-machine.md` once implementation begins; this cookbook intentionally avoids duplicating a second competing list.

Test transitions independently of GitHub and Claude.

## 7. Implement GitHub discovery

The scheduler should poll GitHub rather than require a public webhook service.

Initial eligibility rule:

```text
open Issue
+ ai-ready label
+ no blocking dependency
+ no active execution
```

Sort candidates FIFO.

Configure:

```yaml
execution:
  maxConcurrent: 1
  queue: fifo
```

Do not build a priority engine.

Do not build a second ticketing system.

GitHub Issues, Projects, milestones, labels, comments and PRs remain the human-visible workflow system.

## 8. Implement execution claiming

When the scheduler selects an Issue:

1. Re-read the Issue immediately.
2. Verify it is still open and eligible.
3. Check SQLite for an active execution.
4. Atomically claim/create the execution.
5. Only then start work.

This prevents duplicate execution if the polling loop observes the same Issue more than once.

The one-concurrent-execution limit makes the initial implementation much simpler, but the claim invariant should still exist.

## 9. Implement the Git/worktree manager

For each execution:

```text
Issue #123
    ↓
feature branch
    ↓
isolated worktree
    ↓
Claude works only there
```

Never allow autonomous implementation to operate directly in the user's normal working tree.

The Git manager should provide explicit operations for:

- detecting repository state;
- creating a branch;
- creating a worktree;
- checking status;
- committing;
- pushing;
- locating the PR;
- cleanup.

All destructive operations should be guarded by the permission policy.

## 10. Implement the Claude adapter

Create a thin adapter around the Claude Agent SDK.

The orchestrator should own:

- execution ID;
- phase;
- working directory;
- permissions;
- model role;
- current task context;
- session ID;
- interruption/recovery decisions.

Claude should own:

- investigation;
- reasoning;
- implementation;
- testing;
- technical decisions within authorized scope.

Do not duplicate Claude's agent loop inside the orchestrator.

The adapter should make it possible to:

```text
start session
send task context
receive events/results
interrupt
resume when supported
terminate
capture session metadata
```

Keep this boundary thin so that the orchestrator is not tightly coupled to undocumented Claude Code internals.

## 11. Implement the instruction hierarchy

Create the Claude-side instruction structure described in `claude-agent-instruction-architecture.md`.

Separate:

```text
core rules
project rules
phase rules
task context
human conversation
```

Do not create a single giant dynamic prompt containing everything.

The task-specific context should include only information needed for the current execution.

## 12. Implement intake before implementation

The first real agent workflow should be intake.

Claude receives:

- Issue title/body;
- relevant Issue comments;
- repository state;
- relevant LLM Wiki context;
- project instructions.

Claude must determine:

- what the human actually wants;
- scope;
- acceptance criteria;
- affected areas;
- constraints;
- ambiguities;
- missing information;
- dependencies;
- whether implementation can safely begin.

If unclear, Claude posts a concise GitHub question and enters `WAITING_FOR_HUMAN`.

Do not let the agent begin coding merely because an Issue has `ai-ready`.

## 13. Implement the human conversation protocol

During intake, comments are conversational inputs.

Multiple human comments belong to one answer batch when they occur before the configured completion keyword.

Example:

```text
Agent: Question
Human: answer part 1
Human: additional detail
Human: correction
Human: DONE
```

The orchestrator sends the combined answer to Claude.

Outside intake, comments are queued until the execution reaches a safe point where new human input is accepted.

Commands such as `PAUSE`, `STOP`, `RESUME` and `RESTART` are deterministic orchestrator commands, not natural-language requests that Claude must infer.

Use the exact command syntax defined by `github-workflow-and-conventions.md` and `human-agent-conversation-protocol.md`.

## 14. Implement planning and triage

After intake, Claude should classify the task:

### Execute

The task is sufficiently clear and small enough to implement.

### Plan/approve

The task is understood but needs an implementation plan and human approval before code changes.

### Decompose

The task is too broad and should be split into GitHub child Issues.

Prefer GitHub-native parent/child relationships rather than maintaining a parallel decomposition database.

## 15. Implement the approval boundary

For tasks requiring explicit planning approval:

```text
Claude creates plan comment
        ↓
WAITING_FOR_HUMAN
        ↓
human approves
        ↓
implementation
```

Do not create an unnecessary approval service.

The GitHub Issue conversation is the approval record.

## 16. Implement phase-specific permissions

Permissions must be enforced by mechanisms stronger than prompts.

Use Claude Code/Agent SDK permissions and, where appropriate, hooks such as `PreToolUse` to reject unauthorized operations.

Example policy:

```text
INTAKE
  source writes: denied
  commits: denied
  push: denied

PLANNING
  source writes: denied

IMPLEMENTATION
  source writes: allowed
  tests: allowed
  commits: allowed
  push: allowed according to policy

RELEASE
  merge: human-controlled
  deployment: GitHub Actions
```

Keep the actual policy configurable.

## 17. Implement LLM Wiki access

Treat the Wiki as a Markdown filesystem.

Canonical Wiki content is read-only for the development agent.

The agent may write proposed durable knowledge to:

```text
/inbox/
```

The agent should search and read only relevant documents rather than loading the entire Vault into every context.

Never allow transient implementation notes to silently overwrite canonical Wiki knowledge.

## 18. Implement the first autonomous development loop

The first end-to-end target is intentionally simple:

```text
Issue with ai-ready
    ↓
FIFO scheduler
    ↓
execution claim
    ↓
worktree
    ↓
intake
    ↓
implementation
    ↓
tests
    ↓
commit
    ↓
push
    ↓
PR
```

Do not implement autonomous review yet unless the basic loop is reliable.

## 19. Integrate GitHub Actions

CI remains GitHub-native.

Claude pushes a branch.

GitHub Actions performs:

- install
- lint
- tests
- build
- other repository-specific checks

The orchestrator observes results.

It should not become a CI server.

## 20. Implement review/fix loop

Once the basic loop is reliable:

```text
PR
 ↓
CI
 ↓
independent AI review
 ↓
issues?
 ├── yes → fix → CI/review
 └── no  → human
```

Use a fresh Claude context for independent review when practical.

Bound the number of automated review/fix iterations.

Human merge remains the final approval boundary.

## 21. Define completion

The chosen simple rule is:

> PR merge means the task is resolved.

After detecting merge:

1. record completion;
2. update execution state;
3. update/verify Issue state;
4. clean up worktree;
5. preserve audit information;
6. release the scheduler slot.

Do not infer completion merely because Claude says it is finished.

## 22. Implement handover

Maintain a semantic handover document for active executions.

It should capture:

- current understanding;
- important discoveries;
- decisions;
- completed work;
- current work;
- remaining work;
- unresolved questions;
- exact continuation guidance.

Update it at meaningful checkpoints rather than every interaction.

The handover is a semantic recovery artifact, not the primary runtime database.

## 23. Implement mechanical recovery

SQLite records:

- execution state;
- session state;
- process information;
- timestamps;
- last known checkpoint;
- recovery attempts;
- errors.

When Claude crashes:

```text
process failure
    ↓
record failure
    ↓
create/update recovery context
    ↓
start recovery session
    ↓
verify repository state
    ↓
verify handover
    ↓
continue or fail safely
```

Never assume the filesystem is in the state Claude intended. The recovery session must inspect actual Git/worktree state.

## 24. Implement stop/pause/restart semantics

Distinguish:

### Pause

Temporary interruption; execution can resume.

### Stop

Execution is terminated and cannot simply continue as the same active session.

### Restart

Creates a new execution lineage from a stopped/failed execution, using the persisted handover and repository state.

### Completed

Cannot be restarted as the same task execution after successful resolution.

Closing an active Issue is a stop signal. Reopening an Issue does not automatically restart it; explicit reauthorization is required.

## 25. Implement stale-session detection

The orchestrator should periodically detect executions whose process/session appears dead or whose heartbeat has expired.

Do not immediately assume failure.

Use a sequence:

```text
stale suspicion
    ↓
verify process/session
    ↓
verify recent events
    ↓
classify
    ↓
recover / wait / fail
```

Persist every recovery decision.

## 26. Add CLI observability

Before building any dashboard, implement commands such as:

```bash
ai-orchestrator status
ai-orchestrator executions
ai-orchestrator logs
ai-orchestrator doctor
ai-orchestrator version
```

The most important command is `status`.

It should answer:

- what is running;
- which Issue is active;
- which phase is active;
- which Claude session is active;
- whether the agent is waiting for a human;
- what it is waiting for;
- whether recovery is occurring;
- whether the scheduler is idle.

## 27. Test the system as a state machine

Do not test only happy-path functions.

Create tests for:

- duplicate polling;
- Issue closing while active;
- Issue reopening;
- comments arriving during implementation;
- multiple comments before `DONE`;
- command comments;
- Claude crash;
- orchestrator crash;
- stale session;
- lost network connection;
- Git dirty worktree;
- failed commit;
- failed push;
- failed CI;
- failed review;
- PR merge;
- restart after failure;
- invalid state transitions.

The most valuable tests are recovery and idempotency tests.

## 28. Dogfood the system

Once MVP-1 works, stop manually implementing orchestrator features whenever practical.

Create GitHub Issues describing the next orchestrator capabilities.

Mark them `ai-ready`.

Let the orchestrator build itself.

This provides the strongest possible validation because the system's own development workload exercises its:

- intake protocol;
- planning;
- Git workflow;
- permissions;
- CI;
- review;
- recovery;
- documentation practices.

## 29. Update artifacts from implementation findings

The architecture documents are living technical decisions.

When implementation reveals a problem:

1. create a GitHub Issue describing the finding;
2. identify which artifact is affected;
3. document the observed behavior;
4. decide whether the architecture changes;
5. update the affected artifact;
6. update `artifacts/README.md` if needed;
7. implement the corresponding code change;
8. record the decision in the Issue/PR.

Never let the code silently diverge from the documented architecture.

## 30. Recommended implementation order

Use this order unless actual findings justify a change:

```text
01. repository/bootstrap
02. configuration
03. SQLite + migrations
04. state machine
05. event/audit system
06. GitHub adapter
07. scheduler + FIFO claiming
08. Git/worktree manager
09. Claude Agent SDK adapter
10. basic permissions
11. intake
12. conversation protocol
13. planning/approval
14. implementation loop
15. PR lifecycle
16. GitHub Actions observation
17. completion/cleanup
18. handover
19. recovery
20. stop/pause/restart
21. stale-session detection
22. independent review
23. CI-fix loop
24. hardening
```

Do not skip directly to autonomous review or multi-agent behavior before the basic execution lifecycle is reliable.

## 31. Definition of a trustworthy MVP

The MVP is not successful merely because Claude can modify a repository.

It is successful when the orchestrator can reliably answer:

> Which Issue am I executing, what state is it in, what is Claude doing, what authority does it currently have, what happens if Claude dies, and how does the task eventually reach a human-reviewed merged PR?

That is the minimum trustworthy autonomous-development loop.

## 32. What not to build yet

Do not introduce these unless a concrete requirement emerges:

- Redis
- Kafka
- Kubernetes
- cloud workers
- distributed queues
- custom vector database
- custom ticketing system
- custom CI server
- web dashboard
- multi-user locking system
- multi-agent swarm
- sophisticated priority engine
- custom MCP infrastructure for the Wiki

The initial system is intentionally a local single-user orchestrator.

## 33. Engineering rule for future changes

When deciding whether to add a component, ask:

1. What concrete problem does it solve?
2. Can GitHub, SQLite, Git, Claude Code, or the filesystem already solve it?
3. Does it improve reliability enough to justify another dependency?
4. Can the behavior be tested deterministically?
5. Does it make recovery easier or harder?
6. Does it preserve the separation between GitHub, repository state, Wiki knowledge and runtime state?

If an existing primitive is sufficient, use it.

## 34. Final implementation philosophy

Build the system in small vertical slices.

At every stage:

```text
implement
  ↓
test
  ↓
observe
  ↓
dogfood
  ↓
learn
  ↓
document
  ↓
refine
```

The objective is not to predict the perfect autonomous developer architecture in advance.

The objective is to create a **small, reliable experimental platform that can progressively teach us how autonomous software development works in practice**.
