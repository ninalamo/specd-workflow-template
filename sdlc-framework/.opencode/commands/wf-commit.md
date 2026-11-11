---
name: wf-commit
skill: wf-commit
description: Manage authorized commits and remote synchronization for work completed in a task worktree. Verifies approved specification linkage, inspects the complete diff, validates scope, checks tests and review findings, excludes secrets, creates authorized commits, pushes authorized branches. Never force-push by default.
---

# @wf-commit

Invoke the [wf-commit skill](../skills/wf-commit/SKILL.md) to commit and push work completed in a task worktree.

## Usage

```
@wf-commit <project name/path> <worktree path/branch> <task spec ID> [push yes/no]
```

## What it does

1. Verifies approved specification linkage (no commit without approved task spec)
2. Inspects the complete diff (read-only)
3. Validates intended scope (only task-related files)
4. Checks tests and review findings (tests pass, review approved)
5. Excludes unrelated files and secrets (.env, keys, credentials)
6. Prepares a meaningful commit message referencing the task spec ID
7. Requires explicit user authorization before committing (Gate 1)
8. Creates the authorized commit and records the SHA
9. Inspects remote state (ahead/behind status)
10. Requires explicit user authorization before pushing (Gate 2)
11. Never force-pushes by default
12. Verifies synchronization (remote matches local)
13. Updates Work Ledger and task handoff file

## Never

- Force-pushes by default
- Commits without explicit user authorization
- Pushes without explicit user authorization
- Includes secrets or unrelated files in commits

## Related commands

`@wf-task-code` `@wf-task-noncode` `@wf-review`
