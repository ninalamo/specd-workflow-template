---
name: wf-state
skill: wf-state
description: Discover the actual current state of a project through read-only inspection. Produces an evidence-based project-state report. Does not resolve conflicts or implement issues.
---

# @wf-state

Invoke the [wf-state skill](../skills/wf-state/SKILL.md) to discover the actual current state of a project.

## Usage

```
@wf-state [project name/path] [scope]
```

Scope options: `full` (default), `specs`, `git`, `work-items`, `handoffs`

## What it does

1. Discovers existing, draft, unsigned-off, approved, and superseded specifications
2. Discovers work ledger items by status (pending, in_progress, blocked, done, etc.)
3. Inspects Git state (branches, worktrees, uncommitted changes, unpushed commits)
4. Discovers implementation status and binding.yaml alignment
5. Identifies handoffs, unresolved decisions, floating work, and PR findings
6. Produces an evidence-based project-state report
7. Records new discoveries as Work Ledger items (status: `proposed`)

## Never

- Modifies specs, code, configs, or resolves conflicts
- Implements discovered issues
- Auto-closes existing work ledger items

## Related commands

`@wf-bootstrap` `@wf-reconcile` `@wf-task-code` `@wf-task-noncode` `@wf-review` `@wf-commit`
