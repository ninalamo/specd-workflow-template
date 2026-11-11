---
name: wf-task-noncode
skill: wf-task-noncode
description: Create an implementation-ready specification for a non-coding task (documentation, configuration, infrastructure, process, data migration setup). Follows the plan-fix calibration pattern. No test scenarios. Produces a task spec, per-task worktree and branch, and a task-specific handoff with a suggested skill run.
---

# @wf-task-noncode

Invoke the [wf-task-noncode skill](../skills/wf-task-noncode/SKILL.md) to create an implementation-ready specification for a non-coding task.

## Usage

```
@wf-task-noncode <project name/path> <work description>
```

## What it does

**Calibration (six questions, one at a time — no defaults assumed):**
1. Task shape (documentation, configuration, infrastructure, process, data migration setup)
2. Evidence/deliverables (documents, config files, infra provisioning, sign-offs)
3. Verification depth (review sign-off, config validation, infra smoke test, etc.)
4. Workflow path (worktree needed, branch naming, worktree placement)
5. Implementation surface (what to touch, what's off-limits)
6. Orientation target (who must understand the spec)

**Then:**
7. Explicit project-spec targeting (user selects from presented options — never assumed)
8. Identifies reusable capabilities from spec-workspace library
9. Examines existing implementation (read-only)
10. Defines scope, exclusions, expected behavior, acceptance criteria (non-code deliverables)
11. Routes to independent review (wf-review)
12. Requires explicit human approval
13. Creates a dedicated per-task worktree and branch (per calibration choice 4)
14. Produces a task-specific handoff file with a **suggested skill run**
15. Presents a planned task summary to the user

## Never

- Includes test scenarios (not applicable to non-coding work)
- Writes implementation code
- Creates worktrees for unsigned-off specifications
- Shares worktrees across tasks

## Related commands

`@wf-task-code` `@wf-review` `@wf-commit` `@wf-bootstrap`
