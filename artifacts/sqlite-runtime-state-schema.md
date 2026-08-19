# SQLite Runtime State Schema

## Purpose

SQLite stores orchestrator runtime state, not project requirements or durable project knowledge.

## Proposed tables

### `executions`

- `id` — internal execution identifier
- `issue_number`
- `repository`
- `state`
- `phase`
- `queue_position` / discovery timestamp
- `claude_session_id`
- `worktree_path`
- `branch_name`
- `started_at`
- `updated_at`
- `paused_at`
- `completed_at`
- `failure_reason`
- `parent_execution_id` when relevant

### `events`

Append-only audit/event stream:

- `id`
- `execution_id`
- `event_type`
- `source`
- `external_id`
- `payload_json`
- `created_at`

A uniqueness constraint on `(source, external_id)` should prevent duplicate processing where possible.

### `questions`

- `id`
- `execution_id`
- `issue_number`
- `question_comment_id`
- `question_text`
- `status` (`open`, `answered`, `cancelled`)
- `opened_at`
- `answered_at`

### `comment_batches`

Represents human comments grouped into one answer:

- `id`
- `execution_id`
- `question_id`
- `status` (`collecting`, `ready`, `consumed`)
- `completion_keyword`
- `created_at`
- `completed_at`

### `comment_batch_items`

- `batch_id`
- `github_comment_id`
- `author`
- `body`
- `created_at`

### `worktrees`

- `execution_id`
- `path`
- `branch`
- `base_ref`
- `created_at`
- `cleanup_at`
- `cleanup_status`

### `recovery_attempts`

- `id`
- `execution_id`
- `attempt_number`
- `reason`
- `checkpoint_reference`
- `started_at`
- `completed_at`
- `result`

### `commands`

- `id`
- `execution_id`
- `github_comment_id`
- `command`
- `status`
- `created_at`
- `processed_at`

## State ownership

SQLite is the authoritative store for whether the orchestrator believes an execution is running, waiting, paused, recovering or terminal. GitHub remains authoritative for external human workflow signals. Reconciliation resolves divergence rather than silently overwriting either source.

## Design rules

- Use foreign keys.
- Store timestamps in UTC.
- Store structured payloads as JSON only when a normalized field is not needed for queries.
- Never store secrets in SQLite.
- Prefer append-only events for diagnostics.
- Keep schema small until actual queries demonstrate a need for more tables.
