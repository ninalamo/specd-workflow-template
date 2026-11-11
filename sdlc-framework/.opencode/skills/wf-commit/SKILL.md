---
name: wf-commit
id: wf-commit
version: 1.0.0
description: Manage authorized commits and remote synchronization for work completed in a task worktree. Verifies approved specification linkage, inspects the complete diff, validates intended scope, checks tests and review findings, excludes unrelated files and secrets, prepares meaningful commit messages, creates authorized commits, pushes authorized branches, verifies synchronization, and updates the Work Ledger and handoff. Never force-push by default.
category: git-operations
status: active
framework: sdlc-framework
repository: sdlc-framework
---

# wf-commit

## Purpose
Manage authorized commits and remote synchronization for work completed in a task worktree (created by wf-task-code or wf-task-noncode). Verifies approved specification linkage, inspects the complete diff, validates intended scope, checks tests and review findings, excludes unrelated files and secrets, prepares meaningful commit messages, creates authorized commits, pushes authorized branches, verifies synchronization, and updates the Work Ledger and task handoff.

## Invocation Conditions
Invoke when:
- Implementation work is complete in a task worktree (created by wf-task-code/wf-task-noncode)
- The task specification linked to the worktree is approved
- User wants to commit and/or push the changes

Do not invoke when:
- No task worktree exists for the work
- The linked task specification is not approved (status != Approved)
- No changes have been made in the worktree

## Preconditions
- Task worktree exists with an approved task specification (status: Approved)
- Git repository accessible
- Remote configured (if push is requested)

## Required Context
- Project name or path
- Worktree path or branch name
- Task spec ID linked to the worktree
- Whether to push to remote

## Inputs
| Input | Description | Source |
|-------|-------------|--------|
| Project name / path | Target project | User |
| Worktree path / branch | The worktree to commit from | User |
| Task spec ID | The approved spec linked to this worktree | User (from task handoff file) |
| Push flag | Whether to push to remote (yes/no) | User |

## Outputs
| Output | Description |
|--------|-------------|
| Authorized commit | Commit with meaningful message referencing task spec ID |
| Commit identifier | SHA recorded in work ledger |
| Remote sync verification | Push status (if push requested) |
| Work ledger update | Commit recorded (type: Task, status: done) |
| Task handoff update | Commit SHA and push status recorded in task handoff file |

## Agent Responsibilities
- Verify approved specification linkage (task spec must be Approved) — no commit without approved spec
- Inspect the complete diff (read-only review of all changes)
- Validate intended scope (only files related to the task spec)
- Check tests and review findings (tests pass, independent review approved)
- Exclude unrelated files and secrets (no .env, no keys, no credentials, no unrelated changes)
- Prepare meaningful commit messages (references task spec ID)
- Create authorized commits — only with explicit user authorization
- Verify commit identifiers (SHA recorded)
- Inspect remote state (ahead/behind status)
- Push authorized branches — only with explicit user authorization
- Never force-push by default
- Verify synchronization (remote matches local)
- Update Work Ledger and task handoff file
- Preserve existing files and user changes

## Step-by-Step Procedure

### Step 1 — Verify approved specification linkage
1. Locate the task specification linked to the worktree/branch (from the task handoff file or worktree branch name).
2. Confirm the task spec file exists at `spec-workspace/projects/<project-name>/tasks/task-<nnn>-<type>.md`.
3. Confirm `status: Approved` in the frontmatter.
4. If not approved, error: cannot commit implementation without an approved task specification (enforces Specification Before Implementation invariant).

### Step 2 — Inspect the complete diff
1. In the worktree, run `git status`, `git diff`, and `git diff --cached` (read-only).
2. Review all changed, added, renamed, and deleted files.
3. Document the full set of changes for scope validation.

### Step 3 — Validate intended scope
1. Confirm all changed files are related to the task spec scope (refer to the task spec's scope/exclusions and implementation surface from calibration).
2. Reject any unrelated files — ask the user whether to exclude them or if the task scope needs updating.
3. Confirm no changes outside the worktree scope.

### Step 4 — Check tests and review findings
1. Confirm independent review (wf-review) was approved for the task spec.
2. Confirm any blocking review findings have been resolved.
3. For coding tasks: confirm test scenarios from the task spec have been implemented and pass.
4. For non-coding tasks: confirm verification requirements (review sign-off, config validation, etc.) are met.

### Step 5 — Exclude unrelated files and secrets
1. Scan for secrets and credentials (.env, *.key, *.pem, tokens, passwords, API keys, connection strings).
2. Exclude any secrets from the commit — warn the user and ask for confirmation before including anything questionable.
3. Exclude any unrelated files (build artifacts, node_modules, .gitkeep changes unrelated to the task, etc.).
4. Stage only validated, task-related files.

### Step 6 — Prepare commit message
1. Format: `[WORK-<nnn>] <task summary>` or per project AGENTS.md convention if present.
2. Reference the task spec ID in the commit body.
3. Include a scope summary (what was changed and why).
4. Present the proposed commit message to the user for approval.

### Step 7 — Create authorized commit (Gate 1)
1. Obtain explicit user authorization before committing. Do not treat inferred approval as sufficient.
2. Stage only the validated files.
3. Create the commit with the approved message.
4. Record the commit SHA.

### Step 8 — Verify commit identifier
1. Confirm the commit was created successfully (`git log -1`).
2. Record the commit SHA in the work ledger.
3. Verify the commit is on the correct branch.

### Step 9 — Inspect remote state
1. Check the remote tracking branch (`git branch -vv`, `git remote -v`).
2. Determine ahead/behind status relative to remote.
3. Report remote state to the user.

### Step 10 — Push authorized branch (Gate 2, if push requested)
1. Obtain explicit user authorization before pushing.
2. Never force-push by default. If the push would require force (diverged history), warn the user and ask how to proceed — do not force-push silently.
3. Push the authorized branch to the remote.

### Step 11 — Verify synchronization
1. Confirm remote and local are in sync after push (`git status`, `git log`).
2. Confirm the branch is tracking the remote correctly.
3. Report synchronization status to the user.

### Step 12 — Update Work Ledger and task handoff
1. Record the commit SHA in the work ledger (type: Task, status: done, source: task spec ID).
2. Update the task-specific handoff file with:
   - Commit SHA
   - Branch name
   - Push status (if applicable)
   - Synchronization status
3. Mark the task as ready for the next phase (e.g., next dependent task, or PR creation).

## Decision Rules
| Rule | Statement |
|------|-----------|
| Approved spec required | No commit without an approved task specification linked to the worktree |
| Explicit authorization | No commit or push without explicit user authorization |
| No force-push | Never force-push by default; warn and ask if force would be required |
| Scope validation | Only task-related files in the commit |
| No secrets | Exclude secrets and credentials from commits |
| Human owns authorization | Explicit approval required for both commit and push |

## Delegation Strategy
- None — git operations are handled directly by this skill

## Approval Gates
| Gate | What | Who |
|------|------|-----|
| Gate 1 | Commit authorization | User |
| Gate 2 | Push authorization | User |

## Safety Boundaries
- Never force-push by default
- Never commit without explicit user authorization
- Never push without explicit user authorization
- Exclude secrets and unrelated files from commits
- Preserve existing work and uncommitted changes outside the task scope
- Never discard uncommitted work

## Error Handling
| Situation | Handling |
|-----------|----------|
| Task spec not approved | Error; cannot commit without approved spec |
| No changes to commit | Report "nothing to commit"; exit cleanly |
| Secrets detected | Exclude; warn user; ask for confirmation before including |
| Unrelated files detected | Exclude; ask user whether to adjust task scope |
| Push rejected (diverged history) | Report remote state; warn against force-push; ask user how to proceed |
| Commit fails | Record as Blocker in work ledger |
| Remote not configured | Report; proceed with local commit only if user confirms |

## Validation
- Commit exists with task spec ID reference in message
- Commit SHA recorded in work ledger
- Push verified (if requested)
- Work ledger and task handoff file updated with commit info
- No secrets or unrelated files in commit
- No uncommitted work discarded

## Handoff Requirements
- Commit SHA
- Branch name
- Push status (if applicable)
- Synchronization status
- Next recommended action (e.g., create PR, start next dependent task)

## Related Skills
- **wf-task-code** — creates the worktree this skill commits from
- **wf-task-noncode** — creates the worktree this skill commits from
- **wf-review** — approves the task spec before commit
