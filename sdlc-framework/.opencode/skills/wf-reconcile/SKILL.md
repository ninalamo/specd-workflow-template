---
name: wf-reconcile
id: wf-reconcile
version: 1.0.0
description: Evaluate the relationships and consistency of project specifications. Identifies duplicates, conflicts, overlaps, fragmentation, outdated specs, and missing relationships. Produces a reconciliation plan with recommendations. Never silently modifies or discards approved specifications.
category: reconciliation
status: active
framework: sdlc-framework
repository: sdlc-framework
---

# wf-reconcile

## Purpose
Evaluate the relationships and consistency of project specifications. Identifies duplicates, conflicts, overlaps, fragmentation, outdated specifications, and missing relationships. Produces a **reconciliation plan** with recommended actions (consolidate, revise, split, merge, supersede, no action).

This skill is read-only with respect to specification files. It **never** silently modifies or discards approved specifications. All recommendations require explicit human decision before execution.

## Invocation Conditions
Invoke when:
- The user suspects specification inconsistencies (duplicates, conflicts, overlaps)
- After wf-state discovers multiple or conflicting specifications
- Before creating new specifications that might overlap with existing ones
- Periodically to detect architecture/requirement drift

Do not invoke when:
- No specifications exist for the project

## Preconditions
- At least one project specification exists in `spec-workspace/projects/<project-name>/`
- Work ledger accessible

## Required Context
- Project name or path
- Optional scope: specific spec IDs, or all specs for a project

## Inputs
| Input | Description | Default |
|-------|-------------|---------|
| Project name / path | Target project whose specifications to reconcile | Current working context |
| Scope | `all` or specific spec IDs | `all` |

## Outputs
| Output | Description |
|--------|-------------|
| Reconciliation report | Evidence-based findings: duplicates, conflicts, overlaps, fragmentation, outdated specs, missing relationships |
| Reconciliation plan | Recommended actions (consolidate, revise, split, merge, supersede, no action) each with rationale |
| Work ledger items | New `WORK-XXX` items for each recommended reconciliation action |

## Agent Responsibilities
- Read-only inspection of all project specifications
- Pairwise comparison of specifications for consistency
- Evidence collection — every finding cites spec IDs, file paths, and specific conflicting text
- Recommend actions, never execute them without explicit human approval
- **Never** silently modify or discard approved specifications
- **Never** create, revise, or supersede specifications directly

## Step-by-Step Procedure

### Step 1 — Collect specifications
Gather all specification files for the project from `spec-workspace/projects/<project-name>/`. For each spec record: spec ID, version, status, file path, and last-updated timestamp.

### Step 2 — Detect duplicate specifications
Compare specifications for identical or near-identical scope, objectives, and capabilities. A duplicate is two specs covering the same business capability with overlapping requirements.

For each duplicate pair, record: both spec IDs, versions, statuses, and the overlapping content with evidence quotes.

### Step 3 — Detect conflicting specifications
Compare specifications for contradictory requirements, boundaries, or responsibilities. A conflict is two specs that cannot both be satisfied simultaneously.

For each conflict, record: both spec IDs, the conflicting requirements, evidence quotes from each spec, and severity (critical if both are Approved).

### Step 4 — Detect overlapping requirements
Identify specifications that share partial requirements or capabilities without being full duplicates or direct conflicts. Overlap may be legitimate (shared capability) or problematic (unclear ownership).

### Step 5 — Identify fragmented specifications
Find specifications that split a single logical capability across multiple docs, making it hard to understand the full scope. Fragmentation is a candidate for consolidation.

### Step 6 — Find outdated or superseded specifications
Identify specifications whose `status` is `Superseded` but are still referenced by work items, binding files, or codebases. Also identify `Draft` specs that have been idle beyond a reasonable threshold.

### Step 7 — Identify missing relationships
Check that approved specifications are properly referenced by:
- Work ledger items (source.reference)
- binding.yaml files in codebases
- Task specifications derived from them

### Step 8 — Evaluate architecture and requirement drift
Compare specifications against each other and against any existing implementation (read-only) to detect drift between what is specified and what exists.

### Step 9 — Produce reconciliation plan
For each finding, recommend one of:
- **Consolidate** — merge duplicate/overlapping specs into one
- **Revise** — update a spec to resolve conflict or overlap
- **Split** — divide a fragmented spec into focused specs
- **Merge** — combine related specs that should be one
- **Supersede** — mark outdated specs as superseded with a replacement reference
- **No action** — overlap is legitimate; document as such

Each recommendation includes: affected spec IDs, rationale, evidence, recommended next action, and whether human approval is required (always required for Approved specs).

### Step 10 — Record in work ledger
Create new `WORK-XXX` items for each reconciliation action:
- Type: Task or Decision
- Status: `proposed` (flagged for human review — never auto-executed)
- Source: reference to the spec IDs involved
- **Do not modify or close existing items**

### Step 11 — Present report and handoff
Output the reconciliation report and plan. Highlight critical findings (especially conflicts between Approved specs). Handoff: next steps require explicit human decision on each recommendation.

## Decision Rules
| Rule | Statement |
|------|-----------|
| Read-only inspection | No specification files are modified during this skill |
| No silent modification | Approved specifications are never silently changed, discarded, or superseded |
| Human owns reconciliation decisions | The agent recommends; the human decides and executes |
| Evidence required | Every finding cites spec IDs, file paths, and quoted text |
| Conflict severity | Conflicts between two Approved specs are `critical` |

## Delegation Strategy
- **Specification revision**: If the human approves a consolidation/revision, that work flows through wf-task-code / wf-task-noncode (creating task specs for the revision work)
- **Review**: Disputed reconciliation findings may go to wf-review

## Approval Gates
- No approval gate for discovery/report generation (read-only)
- Any execution of reconciliation recommendations (modifying specs) requires explicit human approval and flows through the appropriate skill gates

## Safety Boundaries
- Never silently modify or discard approved specifications
- Never create, revise, or supersede specifications directly
- Never discard uncommitted work
- Preserve existing files

## Error Handling
| Situation | Handling |
|-----------|----------|
| No specifications found | Report "no specifications to reconcile" |
| Single specification only | Report "no pairs to compare"; no reconciliation needed |
| Spec file unreadable | Report as uninspectable; continue with others |
| Ambiguous overlap | Flag for human judgment; do not auto-resolve |

## Validation
- Reconciliation report contains all finding categories (duplicates, conflicts, overlaps, fragmentation, outdated, missing relationships)
- Every finding cites evidence (spec IDs, file paths, quoted text)
- Reconciliation plan includes recommendations for each finding
- Work ledger contains new `WORK-XXX` items for recommended actions
- No specification files were modified

## Handoff Requirements
- Reconciliation report summary
- Critical findings highlighted (especially Approved-vs-Approved conflicts)
- Recommended actions with rationale
- Next step: human decisions on each recommendation

## Related Skills
- **wf-state** — precedes (discovers specs to reconcile)
- **wf-task-code / wf-task-noncode** — follows if spec revisions are approved (creates task specs for the revision work)
- **wf-review** — may review disputed reconciliation findings
