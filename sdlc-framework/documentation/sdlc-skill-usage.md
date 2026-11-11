# SDLC Skill Usage Guide

This document explains how to invoke the seven SDLC framework skills via OpenCode commands.

## Skill Inventory

| # | Skill ID | Directory | Command | Purpose |
|---|----------|-----------|---------|---------|
| 01 | `wf-bootstrap` | `skills/wf-bootstrap/` | `@wf-bootstrap` | Bootstrap a new project from a natural-language description |
| 02 | `wf-state` | `skills/wf-state/` | `@wf-state` | Discover the current state of a project (read-only) |
| 03 | `wf-reconcile` | `skills/wf-reconcile/` | `@wf-reconcile` | Evaluate specification consistency (duplicates, conflicts, overlaps) |
| 04 | `wf-task-code` | `skills/wf-task-code/` | `@wf-task-code` | Create a coding task specification with test scenarios |
| 05 | `wf-task-noncode` | `skills/wf-task-noncode/` | `@wf-task-noncode` | Create a non-coding task specification (docs, config, infra) |
| 06 | `wf-review` | `skills/wf-review/` | `@wf-review` | Perform independent, evidence-based review |
| 07 | `wf-commit` | `skills/wf-commit/` | `@wf-commit` | Authorized commits and remote synchronization |

All skills live under:
`sdlc-framework/.opencode/skills/<skill-id>/SKILL.md`

All commands live under:
`sdlc-framework/.opencode/commands/wf-<skill-id>.md`

---

## How to Invoke

Each skill is exposed as an OpenCode command prefixed with `@wf-`. Example invocations:

### Start a new project
```
@wf-bootstrap <natural-language project description>
```

### Discover project state
```
@wf-state [project name/path] [scope]
```
Scope options: `full` (default), `specs`, `git`, `work-items`, `handoffs`

### Reconcile specifications
```
@wf-reconcile [project name/path] [scope]
```
Scope options: `all` (default) or specific spec IDs

### Create a coding task specification
```
@wf-task-code <project name/path> <work description>
```
Follows the plan-fix calibration pattern: six choices asked one at a time, recorded bindingly, finalized on explicit confirmation. Produces a task spec with test scenarios, a per-task worktree and branch, and a handoff file with a suggested skill run.

### Create a non-coding task specification
```
@wf-task-noncode <project name/path> <work description>
```
Same calibration pattern as `wf-task-code`, but no test scenarios (not applicable to non-coding work).

### Perform independent review
```
@wf-review <review type> <artifact reference> [scope]
```
Review types: `spec`, `architecture`, `implementation`, `pull-request`

### Commit and push
```
@wf-commit <project name/path> <worktree path/branch> <task spec ID> [push yes/no]
```
Requires explicit authorization for both commit and push. Never force-pushes by default.

---

## End-to-End Flow

```
@wf-bootstrap <project description>
    ↓ (approved project spec + non-executable scaffold)
@wf-task-code <project> <feature description>
    ↓ (approved task spec + worktree + handoff with suggested skill run)
[implementer writes code in worktree]
    ↓
@wf-commit <project> <worktree> <task spec ID> [push yes/no]
    ↓ (commit SHA recorded, work ledger updated)
```

At any point, run `@wf-state` to discover current project state, or `@wf-reconcile` to check specification consistency.

---

## Core Rules

- **No specification, no code**: No implementation worktree or code without an approved specification.
- **Explicit approval**: Human must clearly approve specifications and commits; inferred approval is insufficient.
- **Technology-agnostic**: Specifications never reference languages, frameworks, databases, cloud providers, etc.
- **One worktree per task**: Each task gets its own isolated worktree and branch; worktrees are never shared across tasks.
- **No force-push**: Never force-push by default.
- **No secrets**: Exclude credentials, keys, `.env` files from commits.
- **Handoff includes suggested skill run**: Every task handoff file names the next skill and its exact parameters.
