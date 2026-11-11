---
name: wf-task-code
skill: wf-task-code
description: Create an implementation-ready specification for a coding task (feature, bug fix, refactor, test addition). Follows the plan-fix calibration pattern. Requires explicit project-spec targeting. Produces a task spec with test scenarios, a per-task worktree and branch, and a task-specific handoff with a suggested skill run.
---

# @wf-task-code

Invoke the [wf-task-code skill](../skills/wf-task-code/SKILL.md) to create an implementation-ready specification for a coding task.

## Usage

```
@wf-task-code <project name/path> <work description>
```

## What it does

**Calibration (six questions, one at a time — no defaults assumed):**
1. Task shape (feature, bug fix, refactor, test addition)
2. Evidence/artifacts beyond code
3. Verification depth
4. Workflow path (worktree needed, branch naming, worktree placement)
5. Implementation surface (what to touch, what's off-limits)
6. Orientation target (who must understand the spec)

**Then:**
7. Explicit project-spec targeting (user selects from presented options — never assumed)
8. Identifies reusable capabilities from spec-workspace library
9. Examines existing implementation (read-only)
10. Defines scope, exclusions, expected behavior, acceptance criteria, test scenarios
11. Routes to independent review (wf-review)
12. Requires explicit human approval
13. Creates a dedicated per-task worktree and branch (per calibration choice 4)
14. Produces a task-specific handoff file with a **suggested skill run**
15. Presents a planned task summary to the user

## Never

- Asks about technology stack
- Writes implementation code
- Creates worktrees for unsigned-off specifications
- Shares worktrees across tasks

## Related commands

`@wf-task-noncode` `@wf-review` `@wf-commit` `@wf-bootstrap`
