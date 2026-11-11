---
name: wf-task-code
id: wf-task-code
version: 1.2.0
description: Create an implementation-ready specification for a coding task (feature, bug fix, refactor, test addition). Follows the plan-fix calibration pattern: six choices asked one at a time, answers recorded bindingly, no defaults assumed, finalized only on explicit confirmation. Worktree creation parameters are decided during calibration (choice 4). Requires explicit project-spec targeting (never assumed). Produces a task specification with test scenarios (coding-specific), a dedicated per-task worktree and branch, and a task-specific handoff file that includes a suggested skill run.
category: task-specification
status: active
framework: sdlc-framework
repository: sdlc-framework
---

# wf-task-code

## Purpose
Create an implementation-ready specification for a **coding task** (feature, bug fix, refactor, test addition). Follows the `/plan-fix` calibration pattern: interactive choices asked one at a time, answers recorded bindingly, no defaults assumed, finalized only when you confirm. Worktree creation parameters (worktree needed, branch naming, worktree location) are decided during calibration choice 4. Produces a task specification, coding-specific test scenarios, a dedicated per-task Git worktree and branch, and a task-specific handoff file that includes a **suggested skill run**.

This skill is for coding work only. For non-coding work (docs, config, infra, process), use wf-task-noncode instead.

## Invocation Conditions
Invoke when:
- An approved project specification exists (wf-bootstrap produced it)
- User wants to implement a coding task
- No task specification exists yet for this work unit

Do not invoke when:
- No approved project specification exists (use wf-bootstrap first)
- The work is non-coding (use wf-task-noncode)

## Preconditions
- Approved project specification at `spec-workspace/projects/<project-name>/specification.md` with `status: Approved`
- Work ledger accessible
- Git repository accessible

## Required Context
- Project name or path
- Natural-language description of the coding work

## Inputs
| Input | Description | Source |
|-------|-------------|--------|
| Project name / path | Target project | User |
| Work description | Description of the coding work | User |
| Calibration answers | Six plan-fix-style choices, recorded bindingly one at a time | User (interactive) |
| Target project spec ID | Explicitly selected by user from presented options | User (interactive) |

## Outputs
| Output | Location | Description |
|--------|----------|-------------|
| Calibration run file | `spec-workspace/projects/<project-name>/tasks/task-<nnn>-coding-calibration.md` | Binding record of the six calibration choices, including worktree plan from choice 4 |
| Draft task spec | `spec-workspace/projects/<project-name>/tasks/task-<nnn>-coding.md` | `status: Draft` |
| Approved task spec | Same file, `status: Approved` | Approval evidence recorded in work ledger |
| Dedicated Git branch | Branch named per task spec ID (from calibration choice 4) | Created from base branch |
| Isolated Git worktree | Linked worktree at dedicated path (from calibration choice 4) | One per task only |
| Task-specific handoff file | `spec-workspace/projects/<project-name>/tasks/task-<nnn>-coding-handoff.md` | One per task only — includes detailed handoff + suggested skill run |
| Worktree-spec association | Work ledger + handoff file | Branch and worktree path recorded |
| Planned task summary | Console output + work ledger | Concise summary of the planned task: scope, acceptance criteria, worktree branch/path, suggested skill run |
| Work ledger items | Task created, calibrated, reviewed, approved, worktree-associated | NEW items only; existing items never modified or closed |

## Agent Responsibilities
- **Plan-fix calibration**: Ask six consumption choices one at a time (analogous to plan-fix's scan shape, visual aid, explanation budget, workflow, implementation chrome, teach or orient). Do not assume defaults. Wait for each answer before proceeding.
- Record each answer in the calibration run file; each answer binds subsequent steps.
- **Calibration choice 4 (workflow path)**: Capture worktree/branch parameters — whether a worktree is needed, branch naming preference, worktree placement preference. This is the worktree plan.
- Present the full calibration plan for finalization. Proceed to drafting only when you explicitly confirm the plan is done.
- **Explicit project-spec targeting**: Present all approved project specifications with ID/title/status/last-updated. User must select one. Never assume.
- Identify reusable capabilities from `spec-workspace/library/`
- Examine existing implementation (read-only only)
- Define scope, exclusions, expected behavior, acceptance criteria, test scenarios (coding-specific)
- Submit draft for independent review (wf-review)
- Obtain explicit human approval before worktree creation or implementation
- **Create a dedicated per-task worktree and branch** per the calibration plan (choice 4), after spec approval
- Associate the worktree with the task specification in work ledger and handoff file
- **Generate a detailed task-specific handoff file that includes a suggested skill run** (which skill to run next, with parameters)
- **Present a summary of the planned task to the user** (scope, acceptance criteria, worktree branch/path, suggested skill run)
- Never write implementation code during this skill
- Never modify the approved project specification
- Never discard uncommitted work
- Never force-push by default

## Step-by-Step Procedure

### Step 1 — Plan-fix calibration (six choices, one at a time)
Ask each choice, wait for the answer, record it bindingly in the calibration run file. No defaults. Choices:

1. **Task shape**: What kind of coding work is this (feature, bug fix, refactor, test addition)? What is the estimated scope and blast radius?
2. **Evidence/artifacts**: What artifacts must this task produce beyond code (tests, docs, config, diagrams)?
3. **Verification depth**: How rigorously must this be verified (unit tests, integration tests, manual review, demo)?
4. **Workflow path**: Does this require an isolated worktree (yes/no)? If yes: what branch naming preference? What worktree placement preference? This decision **is** the worktree plan — no separate worktree step later.
5. **Implementation surface**: Which existing code/modules/interfaces will this touch? Which are off-limits?
6. **Orientation target**: Who must understand this task spec (implementer, reviewer, stakeholder, future maintainer)?

### Step 2 — Finalize calibration plan
Present all six recorded answers to the user, including the worktree plan from choice 4. Ask: "Is this calibration plan final, or do you want to revise any choice?" Only proceed to Step 3 when you explicitly confirm the plan is final.

### Step 3 — Explicit project-spec targeting
List all approved project specifications for the project, each with: spec ID, title, status, last-updated timestamp. Ask the user to select which spec this task implements. Record the selected `parent_spec` ID.

### Step 4 — Verify selected spec is Approved
Confirm the selected spec has `status: Approved`. If not, error: direct the user to get the project specification approved first (this skill cannot proceed without an approved parent spec).

### Step 5 — Identify reusable capabilities
Scan `spec-workspace/library/` for capabilities applicable to this task. Record adopted capabilities and their versions in the task spec.

### Step 6 — Examine existing implementation (read-only)
Inspect `generated-projects/<project-name>/` for existing code relevant to this task. Identify dependencies, interfaces, and boundaries already established. Do not modify anything.

### Step 7 — Define scope and exclusions
State what is in-scope and what is explicitly out-of-scope for this coding task, informed by calibration choices 1, 5, and 6.

### Step 8 — Establish expected behavior
Describe expected behavior in technology-agnostic terms. Reference the sections of the approved project specification this task implements.

### Step 9 — Define measurable acceptance criteria
Create measurable, testable criteria that determine when the task is complete. Each criterion must be independently verifiable. No vague criteria.

### Step 10 — Define test scenarios (coding-specific)
Define test scenarios covering happy path, edge cases, and failure modes. Each scenario: input, expected output, verification method. Informed by calibration choice 3 (verification depth).

### Step 11 — Establish verification requirements
Specify how the implementation will be verified (tests, reviews, demonstrations) and what evidence is required for completion.

### Step 12 — Draft task specification
Create `spec-workspace/projects/<project-name>/tasks/task-<nnn>-coding.md` with YAML frontmatter:

```yaml
---
task_id: WORK-<nnn>-TASK-CODE
version: 1.0
status: Draft
project: <project-name>
parent_spec: WORK-<nnn>-SPEC
task_type: code
calibration_file: task-<nnn>-coding-calibration.md
created: <ISO-8601 timestamp>
updated: <ISO-8601 timestamp>
---
```

Body (technology-agnostic):
- Task summary and description
- In-scope and out-of-scope
- Expected behavior
- Acceptance criteria (measurable)
- Test scenarios (coding-specific)
- Verification requirements
- Dependencies (other task specs, capabilities)
- Reusable capabilities adopted (with versions)
- Consistency note: how this task aligns with the approved project specification

### Step 13 — Independent review
Submit the draft task specification to wf-review (or a dedicated reviewer agent). The reviewer must challenge assumptions, missing requirements, defects, security concerns, regression risks, and unnecessary complexity. Every finding needs: evidence, severity, affected location, reasoning, and recommended resolution. The reviewer must not simply agree.

### Step 14 — Approval gate (Gate 3)
Present the draft + review findings to the human. Obtain explicit approval:
- On approval: change task spec frontmatter `status` to `Approved`; record approval as a Work Ledger `Decision` item with evidence (spec ID, version, approver, timestamp, file path)
- On rejection or blocking findings: return to Step 12; address findings; re-draft and re-submit for review

### Step 15 — Per-task worktree and branch creation (per calibration plan choice 4)
**Gate 4**: Worktree creation authorization. Worktree parameters come from calibration choice 4 — this is not a new decision.

1. **Validate specification identity and approval**: Confirm the task spec exists with `status: Approved`.
2. **Inspect Git state (read-only)**: List branches (`git branch -a`), existing linked worktrees (`git worktree list`), current branch, and uncommitted changes (`git status`).
3. **Check for uncommitted work**: If uncommitted changes exist, warn the user. Ask whether to stash, commit, or abort. Never discard uncommitted work.
4. **Determine base branch**: Default to the repository's default branch (main/master). Allow user to specify a different base branch. Confirm it exists locally or can be fetched.
5. **Check for conflicts**: Verify no existing worktree is already associated with this task spec ID. Check for branch name conflicts (a branch with the same name already exists). If conflict exists, ask user how to proceed (new name, reuse existing, or abort).
6. **Create dedicated branch**: Branch name convention from calibration choice 4 (or project AGENTS.md convention if present, or default `task/WORK-<nnn>-<short-slug>`). Create from the base branch.
7. **Create isolated worktree**: Create a linked worktree at the dedicated path from calibration choice 4 (or user-specified location). Check out the new branch in the worktree. Verify the worktree is properly linked.
8. **Associate worktree with specification**: Record the association in the work ledger (`WORK-XXX` item, type: Task, status: done, source: task spec ID) and in the task-specific handoff file.

### Step 16 — Task-specific handoff file (HARD RULE: detailed handoff + suggested skill run)
Create `spec-workspace/projects/<project-name>/tasks/task-<nnn>-coding-handoff.md` containing **only this task's information** and MUST include:

**Detailed handoff:**
- Approved task ID and spec location
- Parent project spec ID
- Calibration run file location
- Worktree branch name and worktree path
- Base branch
- Task scope summary (in-scope, out-of-scope)
- Acceptance criteria summary
- Verification requirements summary
- Any unresolved open questions

**Suggested skill run (MANDATORY):**
- Which skill to run next (e.g., wf-commit after implementation, or wf-task-code for the next dependent task)
- Exact parameters to pass (project name/path, task spec ID, worktree path/branch, push flag yes/no)
- Rationale for why this skill is the logical next step

### Step 17 — Record work ledger entries
Create new `WORK-XXX` items:
- Task created (type: Task, status: done)
- Task calibrated (type: Decision, status: done)
- Task approved (type: Decision, status: done, with approval evidence)
- Worktree associated (type: Task, status: done)
- Never modify or close existing items

### Step 18 — Present planned task summary
Present a concise summary of the planned task to the user:
- Task ID and type (code)
- Parent project spec ID
- Scope summary (in-scope, out-of-scope)
- Acceptance criteria summary
- Test scenarios count (coding-specific)
- Worktree branch name and worktree path
- Base branch
- Suggested skill run (which skill, exact parameters, rationale)
- Any unresolved open questions

This summary is the user's confirmation that the task is fully planned and ready for implementation.

## Decision Rules
| Rule | Statement |
|------|-----------|
| Plan-fix calibration | Six choices asked one at a time; answers recorded bindingly; no defaults assumed |
| Calibration finalization | No drafting until user explicitly confirms the calibration plan is final |
| Worktree in calibration | Worktree/branch parameters decided in calibration choice 4; no separate worktree decision later |
| Explicit spec targeting | User selects project spec from presented options; never assumed |
| Coding only | This skill is for coding tasks; test scenarios are required |
| One worktree per task | Each task spec gets its own dedicated worktree and branch; never share worktrees across tasks |
| Approved spec only | Never create implementation worktrees for unsigned-off specifications |
| No uncommitted work discard | Check for uncommitted changes; warn user before proceeding |
| One handoff per task | Handoff file contains only this task's information |
| HARD RULE: handoff includes suggested skill run | Every task-specific handoff file must include a detailed handoff AND a suggested skill run with parameters |
| Human owns approval | Explicit approval required before worktree creation and implementation |

## Delegation Strategy
- Independent review: Delegated to wf-review or a dedicated reviewer agent
- Commit and push: Delegated to wf-commit after implementation

## Approval Gates
| Gate | What | Who |
|------|------|-----|
| Gate 1 | Calibration plan finalization (includes worktree plan) | User |
| Gate 2 | Independent review of draft task spec | Reviewer agent / wf-review |
| Gate 3 | Explicit task specification approval | Human |
| Gate 4 | Worktree creation authorization | Human |

## Safety Boundaries
- No implementation code written during this skill
- Technology-agnostic task specification (no language, framework, database, etc.)
- Never modify the approved project specification
- One worktree per task; never share worktrees across tasks
- Never discard uncommitted work
- Never force-push by default

## Error Handling
| Situation | Handling |
|-----------|----------|
| No approved project specification | Error; direct user to wf-bootstrap first |
| No specifications to select | Error; project needs bootstrapping before task specification |
| Calibration plan not finalized | Pause; do not proceed to drafting |
| Approval denied | Return to drafting; address review findings; re-submit |
| Blocking review findings | Address before re-submission for review |
| Uncommitted changes present | Warn user; ask to stash, commit, or abort; never discard |
| Branch already exists | Ask user: new name, reuse existing, or abort |
| Worktree already exists for this task | Warn; do not duplicate; ask whether to reuse or abort |
| Worktree creation fails | Record as Blocker in work ledger |

## Validation
- Calibration run file exists with all six answers recorded bindingly, including worktree plan from choice 4
- Task spec exists with `status: Approved` and `task_type: code`
- Parent spec ID references an Approved project specification
- Test scenarios present (coding requirement)
- Dedicated branch exists, created from base branch per calibration choice 4
- Isolated worktree exists at dedicated path per calibration choice 4, checked out on new branch
- Worktree-spec association recorded in work ledger and handoff file
- Task-specific handoff file exists containing only this task's information, with detailed handoff AND suggested skill run
- No existing work ledger items were modified or closed
- No uncommitted work was discarded

## Handoff Requirements
- Approved task ID and spec location
- Parent project spec ID
- Calibration run file location
- Branch name and worktree path
- Base branch
- Next recommended action
- Suggested skill run with exact parameters
- Any unresolved open questions carried forward

## Related Skills
- **wf-bootstrap** — creates the approved project specification
- **wf-review** — review gate for the draft task spec
- **wf-commit** — commits task spec and implementation from the worktree (suggested skill run in handoff)
