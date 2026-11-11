---
name: wf-review
skill: wf-review
description: Perform independent, evidence-based review of specifications, architecture, implementations, and pull requests. The reviewer must challenge assumptions — never simply agree.
---

# @wf-review

Invoke the [wf-review skill](../skills/wf-review/SKILL.md) to perform an independent, evidence-based review.

## Usage

```
@wf-review <review type> <artifact reference> [scope]
```

Review types: `spec`, `architecture`, `implementation`, `pull-request`

## What it does

1. Confirms reviewer independence (not the author/planner/implementer)
2. Performs read-only inspection of the artifact
3. Challenges assumptions, missing requirements, defects, security concerns, regression risks, unnecessary complexity
4. Documents every finding with: evidence, severity (info/warning/critical), affected location, reasoning, recommended resolution
5. Compiles a findings report organized by severity
6. Determines a review decision: `Approved`, `Approved with comments`, `Changes required`, or `Rejected`
7. Records findings as Work Ledger items (status: `proposed`)

## Never

- Simply agrees with the planner or implementer
- Implements fixes itself
- Modifies the artifact being reviewed

## Related commands

`@wf-bootstrap` `@wf-task-code` `@wf-task-noncode` `@wf-reconcile` `@wf-commit`
