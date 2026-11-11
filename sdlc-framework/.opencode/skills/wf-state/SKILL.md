---
name: wf-state
id: wf-state
version: 1.0.0
description: Discover the actual current state of a project through read-only inspection. Produces an evidence-based project-state report. Does not resolve conflicts, implement issues, or modify any files beyond appending new work ledger discovery items.
category: discovery
status: active
framework: sdlc-framework
repository: sdlc-framework
---

# wf-state

## Purpose
Discover the actual current state of a project through read-only inspection. Produces an **evidence-based project-state report**.

This is primarily a read-only discovery skill. It **does not** resolve specification conflicts, implement discovered issues, or modify any files (except appending new discovery items to the work ledger).

## Invocation Conditions
Invoke when:
- The user wants to know the current state of a project (session resume, handoff, before planning new work)
- At the start of a session to establish context
- A project already exists (has entries in `spec-workspace/projects/` and/or a codebase in `generated-projects/`)

Do not invoke when:
- No project exists yet (use wf-bootstrap instead)

## Preconditions
- Project exists (spec-workspace and/or generated-projects directories present)
- Git repository accessible (if applicable)
- Work ledger accessible (may be initialized if absent)

## Required Context
- Project name or path (optional — defaults to current working context)
- Optional scope filter

## Inputs
| Input | Description | Default |
|-------|-------------|---------|
| Project name / path | Target project to discover | Current working context |
| Scope | `full`, `specs`, `git`, `work-items`, `handoffs` | `full` |

## Outputs
| Output | Description |
|--------|-------------|
| Project-state report | Markdown report with evidence-backed findings, organized by category |
| Work ledger additions | New `WORK-XXX` items for discoveries requiring follow-up |
| Evidence index | File paths, commit SHAs, spec IDs, PR numbers cited per finding |

## Agent Responsibilities
- Read-only inspection of specifications, work ledger, Git state, codebase structure
- Evidence collection — every finding must cite evidence (file path, commit SHA, spec ID, PR number)
- Report generation
- Record discoveries as new Work Ledger items (status: proposed, flagged for human review)
- **Never** auto-close or modify existing work ledger items
- **Never** modify specs, code, configs, or resolve conflicts
- **Never** implement discovered issues

## Step-by-Step Procedure

### Step 1 — Establish project context
1. Confirm project name and scope with the user.
2. Locate `spec-workspace/projects/<project-name>/` and `generated-projects/<project-name>/`.
3. Locate work ledger (`spec-workspace/<project-name>/work_ledger.yaml` or `spec-workspace/project-temp/work_ledger.yaml`).

### Step 2 — Discover specifications
Scan `spec-workspace/projects/<project-name>/` for specification files:
- Draft specifications (`status: Draft`)
- Unsigned-off specifications (in review, no approval evidence)
- Approved specifications (`status: Approved`) with approval evidence
- Superseded specifications (`status: Superseded`)
- Missing specifications (project exists but no spec found)

For each spec record: spec ID, version, status, file path, approval evidence (or absence thereof).

### Step 3 — Discover work ledger state
Read the work ledger and categorize items:
- `pending` / `ready` — work not yet started
- `in_progress` — work started but incomplete
- `blocked` — work with blockers
- `in_review` — work awaiting review
- `done` — completed work
- Missing `next_action` — items without a concrete next step

### Step 4 — Discover Git state
Inspect Git (read-only only):
- Local and remote branches
- Linked worktrees
- Current branch and HEAD state
- Uncommitted changes (modified, staged, untracked)
- Unpushed commits (local commits not on remote)
- Branch vs. remote tracking status

### Step 5 — Discover implementation status
Inspect `generated-projects/<project-name>/`:
- Does a codebase exist?
- Is `binding.yaml` present and referencing a spec ID + version?
- Does the codebase structure match the architecture decision in the spec?
- Any executable code present without an approved spec (potential invariant violation — report but do not act)

### Step 6 — Discover handoffs and unresolved decisions
- Look for handoff records in work ledger or spec directories
- Unresolved decisions / open questions from bootstrap or prior sessions
- Decisions recorded but not yet acted upon

### Step 7 — Identify floating and untracked work
- TODO/FIXME/HACK markers in code (if codebase exists)
- Uncommitted work not reflected in work ledger
- Work items referenced in specs but absent from work ledger

### Step 8 — Discover outstanding PR findings
- Open pull requests associated with the project
- PR review findings (comments, requested changes, approvals)
- Merged PRs not yet reflected in work ledger or specs

### Step 9 — Compile evidence-based report
Organize findings by category:

1. **Specifications** — draft, unsigned-off, approved, superseded, missing
2. **Work items** — by status, with next actions
3. **Git state** — branches, worktrees, uncommitted, unpushed
4. **Implementation status** — codebase existence, binding, spec alignment
5. **Handoffs and unresolved decisions**
6. **Floating / untracked work**
7. **PR findings**

Each finding must include: description, evidence location, severity (info/warning/critical), recommended action.

### Step 10 — Record discoveries in work ledger
Create new `WORK-XXX` items for discoveries requiring follow-up:
- Type: Task, Blocker, Question, Decision, etc.
- Status: `proposed` (flagged for human review — never auto-closed)
- Source: reference to spec/git/work-item that generated the item
- **Do not modify or close existing items**

### Step 11 — Present report and handoff
- Output the project-state report to the user
- Highlight blockers, invariant violations, and items requiring decisions
- Handoff: next recommended action (e.g., wf-reconcile for conflicts, wf-task-code / wf-task-noncode for pending work)

## Decision Rules
| Rule | Statement |
|------|-----------|
| Read-only | No file modifications of any kind (except appending new work ledger discovery items) |
| Evidence required | Every finding must cite evidence (path, SHA, spec ID, PR number) |
| No conflict resolution | Specification conflicts are delegated to wf-reconcile |
| No implementation | Discovered issues are not implemented here |
| Human owns interpretation | The agent reports; the human decides what to do |
| No auto-close | Work ledger items are created as `proposed`, never auto-resolved |

## Delegation Strategy
- **Conflict resolution**: Delegated to wf-reconcile if conflicting specs are found
- **Implementation planning**: Delegated to wf-task-code / wf-task-noncode for approved pending work
- **Git operations**: Delegated to wf-commit if commits are needed (not part of this skill)

## Approval Gates
- No approval gate for discovery itself (read-only)
- If discoveries reveal work needing implementation → that flows through wf-task-code / wf-task-noncode (create task spec), then wf-review (review), then wf-commit (commit).

## Safety Boundaries
- Read-only: no writes to specs, code, or configs; only appending new discovery items to the work ledger is acceptable
- Never discard pending work
- Never force-push (not applicable at discovery)
- Preserve existing files

## Error Handling
| Situation | Handling |
|-----------|----------|
| No specifications found | Report "no specifications discovered"; recommend wf-bootstrap if project is new |
| No Git repository | Report "no Git state available"; proceed with spec and work-ledger discovery only |
| Work ledger missing | Report absence; optionally initialize with current discoveries |
| Project path not found | Error; ask user to confirm project name or path |
| Binary / large files in Git | Skip; report as uninspectable |

## Validation
- Report contains all applicable categories (specs, work items, Git, implementation, handoffs, floating work, PR findings)
- Every finding has an evidence reference
- Work ledger contains new `WORK-XXX` items for follow-up discoveries
- No existing work ledger items were modified or closed
- No files were modified outside of appending new work ledger entries

## Handoff Requirements
- Project-state report summary
- Blocker and invariant-violation highlights
- Next recommended action (wf-reconcile, wf-task-code, or other)
- Any unresolved open questions

## Related Skills
- **wf-bootstrap** — precedes (project must exist before state discovery)
- **wf-reconcile** — follows for specification conflicts
- **wf-task-code / wf-task-noncode** — follow for approved pending work
- **wf-review** — may be needed for disputed findings
- **wf-commit** — follows for committing discovery-driven changes
