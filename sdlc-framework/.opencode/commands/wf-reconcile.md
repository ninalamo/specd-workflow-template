---
name: wf-reconcile
skill: wf-reconcile
description: Evaluate the relationships and consistency of project specifications. Identifies duplicates, conflicts, overlaps, fragmentation, outdated specs, and missing relationships. Never silently modifies or discards approved specifications.
---

# @wf-reconcile

Invoke the [wf-reconcile skill](../skills/wf-reconcile/SKILL.md) to evaluate specification consistency.

## Usage

```
@wf-reconcile [project name/path] [scope]
```

Scope options: `all` (default) or specific spec IDs

## What it does

1. Collects all project specifications
2. Detects duplicate specifications (identical or near-identical scope)
3. Detects conflicting specifications (contradictory requirements)
4. Detects overlapping requirements (partial shared capabilities)
5. Identifies fragmented specifications (single capability split across docs)
6. Finds outdated or superseded specifications still referenced
7. Identifies missing relationships (work ledger, binding.yaml, task specs)
8. Evaluates architecture and requirement drift
9. Produces a reconciliation plan with recommended actions (consolidate, revise, split, merge, supersede, no action)
10. Records recommendations as Work Ledger items (status: `proposed`)

## Never

- Silently modifies or discards approved specifications
- Creates, revises, or supersedes specifications directly

## Related commands

`@wf-state` `@wf-task-code` `@wf-task-noncode` `@wf-review`
