# LLM Wiki Integration Protocol

## Role

The project-specific LLM Wiki Vault is the primary source of durable project knowledge for Claude.

## Access model

- Claude can read the entire Vault.
- Canonical Wiki content is read-only to the coding workflow.
- Claude can write proposals only under `/inbox`.
- Wiki promotion is a separate, manually initiated process.

## Retrieval behavior

Claude should not load the entire Vault into context. It should:

1. inspect the top-level structure;
2. identify relevant domains/components;
3. search by exact terms and concepts;
4. read the smallest set of authoritative documents needed;
5. cross-check current repository state;
6. record important discoveries in handover.

## Authority

Wiki content is durable knowledge, not current task authorization. Current human decisions and approved Issue plans override stale Wiki guidance.

## `/inbox`

Use `/inbox` for proposed durable knowledge such as:

- newly discovered architecture facts;
- project conventions;
- reusable troubleshooting knowledge;
- domain explanations.

Do not put temporary execution chatter into the Wiki.

## No MCP requirement initially

The initial design intentionally uses the local Markdown Vault directly. An MCP/search service can be added only if measured retrieval problems justify it.

## Separation

GitHub = task/workflow truth.
Git = implementation truth.
Wiki = durable knowledge.
SQLite = runtime state.
