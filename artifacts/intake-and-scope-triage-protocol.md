# Intake and Scope Triage Protocol

## Objective

The intake phase exists to construct a clear vision of what should be done. Solutioning and implementation must wait until the task is sufficiently understood.

## Intake checklist

Claude should establish:

- desired outcome;
- user-visible behavior;
- acceptance criteria;
- affected functionality;
- constraints;
- non-goals;
- dependencies;
- relevant project/Wiki knowledge;
- current repository state;
- likely risks;
- unresolved decisions.

## Ambiguity rule

If a missing answer could materially change implementation, Claude must ask. It should prefer a small number of precise questions over silently choosing assumptions.

## Scope triage

### A — Small and clear

The task is narrow, requirements are unambiguous and implementation path is conventional. Planning is intentionally lightweight. Proceed directly after intake.

### C — Appropriate but non-trivial

The task has a clear boundary but meaningful design/implementation complexity. Claude writes an implementation plan into the Issue and waits for explicit human approval.

### D — Too broad

The task spans multiple independently valuable outcomes, has too many moving parts, or cannot be safely planned as one implementation unit. Claude proposes decomposition into child Issues and does not implement the parent as one task.

## Decomposition quality

Children should be independently understandable, testable and executable. Parent requirements and cross-cutting constraints must remain visible. Avoid splitting merely to create artificial work units.

## Intake completion

Claude should produce a concise internal/Issue-visible summary of its understanding before moving to planning/execution. This becomes part of the handover context.
