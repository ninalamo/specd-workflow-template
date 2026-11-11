---
name: wf-bootstrap
id: wf-bootstrap
version: 1.0.0
description: Establish a new project's foundation from a natural-language project description. Produces a technology-agnostic, explicitly approved project specification and a non-executable physical scaffold. Never asks about technology stack. Never writes executable code.
category: project-management
status: active
framework: sdlc-framework
repository: sdlc-framework
---

# wf-bootstrap

## Purpose
Establish a new project's foundation from a natural-language project description. Produces:
1. A **technology-agnostic, explicitly approved project specification** stored in `spec-workspace/projects/<project-name>/specification.md`.
2. A **non-executable physical scaffold** under `generated-projects/<project-name>/` (directory structure only, no executable code).
3. A **project-specific AGENTS.md** that overrides configurable defaults but never weakens protected framework invariants.
4. **Project-level skills** installed into `.opencode/skills/`.
5. **Work ledger entries** recording project state and an initial handoff.

This skill establishes the project as a whole, not individual implementation tasks. It must never ask about the technology stack (languages, frameworks, databases, ORMs, libraries, package managers, cloud providers, hosting platforms, testing frameworks).

## Invocation Conditions
Invoke when:
- The user provides a natural-language project description (idea, objectives, major capabilities, workflows) and wants to start a new project under the SDLC framework.
- No approved project specification exists yet for this project.

Do not invoke when:
- An approved project specification already exists for this project (use wf-state instead).
- The user wants to implement a feature, fix a bug, or refactor (use wf-task-code / wf-task-noncode and downstream skills).

## Preconditions
- The framework repository is available (`sdlc-framework/`, `spec-workspace/`, `generated-projects/`).
- A human is available to give explicit approval at the approval gate.
- The work ledger is accessible (initializable if absent).

## Required Context
- Natural-language project description (from the user).
- Optional: suggested project name.

## Inputs
| Input | Description |
|-------|-------------|
| Project description | Free-form text describing the project idea, objectives, capabilities, and workflows |
| Project name (optional) | Suggested identifier for the project |

## Outputs
| Output | Location | Description |
|--------|----------|-------------|
| Draft project specification | `spec-workspace/projects/<project-name>/specification.md` | YAML frontmatter + body, `status: Draft` |
| Independent review findings | Work ledger + reviewer output | Evidence-based findings, each with evidence/severity/location/reasoning/resolution |
| Approved project specification | `spec-workspace/projects/<project-name>/specification.md` | `status: Approved`, approval evidence recorded |
| Non-executable physical scaffold | `generated-projects/<project-name>/` | Directory structure only, `.gitkeep`/empty placeholders, no executable code |
| Project-specific AGENTS.md | `generated-projects/<project-name>/AGENTS.md` | Overrides configurable defaults, cannot weaken protected invariants |
| Project-level skills | `generated-projects/<project-name>/.opencode/skills/<skill-id>/SKILL.md` | Copies of canonical framework skills |
| binding.yaml | `generated-projects/<project-name>/binding.yaml` | References approved spec ID + version |
| Initial work ledger entries | `spec-workspace/<project-name>/work_ledger.yaml` (or project-temp) | Project-seeded, spec-approved, scaffold-created records |
| Handoff record | Work ledger + console output | Approved spec ID, scaffold location, next recommended action |

## Agent Responsibilities
- Facilitate discovery: business objectives, stakeholders, major capabilities, workflows, non-goals, success criteria.
- Restate the problem in one sentence; record open questions.
- Calibrate architecture via focused questions (application composition, repository topology, deployment organization). Present trade-offs and a recommendation; the human decides.
- Draft the technology-agnostic specification.
- Orchestrate independent review (delegated to wf-review or a reviewer agent). The reviewer must challenge assumptions — not rubber-stamp.
- Enforce the approval gate before any scaffold generation.
- Create the non-executable scaffold, AGENTS.md, install skills, record state.
- Produce a handoff with next actions.
- Never write executable code, tests, or executable configuration before spec approval.
- Never ask about the technology stack.

## Step-by-Step Procedure

### Step 1 — Discovery & framing
1. Restate the project in one sentence and confirm it with the user.
2. Identify and record: business objectives, stakeholders, major capabilities, workflows, non-goals, success criteria.
3. Record open questions that need resolution before drafting.
4. Confirm or adjust the project name with the user.

### Step 2 — Architecture calibration
Ask focused questions **only** when a choice materially affects the project. Ask one important question at a time. For each choice:
- Present the relevant alternatives with trade-offs.
- Offer a recommendation with rationale.
- Let the human decide.

Relevant alternatives:
- Application composition: simple frontend + Web API vs. full-stack application vs. simple monolith vs. modular monolith vs. microservices.
- Logical repository organization: monorepo vs. multiple repositories.
- Deployment organization: single deployment unit vs. independently deployable units.

**Never assume** microservices, modular monoliths, Clean Architecture, layered architecture, or any other predetermined pattern. Architecture decisions emerge from requirements and constraints.

Record the architecture decision in the specification.

### Step 3 — Draft specification
Create `spec-workspace/projects/<project-name>/specification.md` with YAML frontmatter:

```yaml
---
spec_id: WORK-<nnn>-SPEC
version: 1.0
status: Draft
project: <project-name>
created: <ISO-8601 timestamp>
updated: <ISO-8601 timestamp>
---
```

Body must contain (technology-agnostic only):
- Business objectives and stakeholders
- Major capabilities and workflows
- Logical architecture boundaries and responsibilities
- Non-goals and success criteria
- Quality expectations
- Architecture composition decision (from Step 2)
- Repository topology decision (from Step 2)
- Open questions (if any remain)

**Do not reference** programming languages, frameworks, databases, ORMs, libraries, package managers, cloud providers, hosting platforms, or testing frameworks.

### Step 4 — Independent review
Submit the draft specification to independent review:
- Delegate to wf-review or a dedicated reviewer agent.
- The reviewer must challenge assumptions, missing requirements, defects, security concerns, regression risks, and unnecessary complexity.
- Every finding must include: evidence, severity, affected location, reasoning, and recommended resolution.
- The reviewer must not simply agree with the planner.

Collect all review findings.

### Step 5 — Approval gate (Gate 1 & Gate 2)
Present to the human:
1. The draft specification.
2. All independent review findings.

Obtain **explicit** project-specification approval:
- A request to build something is **not** automatic approval of its specification.
- Inferred approval is **not** explicit approval.
- The human must clearly state approval.

On approval:
- Change the specification frontmatter `status` from `Draft` to `Approved`.
- Set `updated` timestamp.
- Record approval as a Work Ledger `Decision` item with: spec ID, version, approver, timestamp, and the specification file path as evidence.

On rejection or blocking findings:
- Return to Step 3; address findings and re-draft.
- Return to Step 4 (independent review) so the reviewer can validate the changes.
- Then proceed to Step 5 for re-approval.
- **Do not proceed to scaffold generation.**

### Step 6 — Non-executable physical scaffold
Create directory structure under `generated-projects/<project-name>/`:

```
generated-projects/<project-name>/
├── .opencode/
│   └── skills/
├── AGENTS.md
├── binding.yaml
├── docs/                          (empty, with .gitkeep)
└── src/                           (empty, with .gitkeep)  -- only if architecture decision implies source directories
```

Rules:
- Directory structure only; `.gitkeep` files or empty directories as placeholders.
- **No executable source code.**
- **No test implementation.**
- **No executable project configuration** (no build files, no package manifests, no runtime configs).
- Preserve existing files and user changes; do not silently discard pending work.

### Step 7 — Project-specific AGENTS.md
Create `generated-projects/<project-name>/AGENTS.md`:

- References the approved spec ID and version.
- Overrides configurable global defaults where the project requires it.
- **Cannot override** protected framework invariants (Specification Before Implementation, Technology Agnosticism, Evidence-Based Completion).
- States that technology binding happens at the codebase implementation level, separate from this specification.

### Step 8 — Install project-level skills
Copy canonical framework skills into `generated-projects/<project-name>/.opencode/skills/<skill-id>/SKILL.md`:
- Include wf-bootstrap itself.
- Include any other canonical skills the project will need.

Rules:
- No global skill dependencies.
- No parent-directory skill dependencies.
- No sibling-repository skill dependencies.
- Every generated codebase owns its required skills.
- Never silently overwrite customized project skills (if a skill already exists at the destination, warn and ask before overwriting).

### Step 9 — Record initial project state & handoff
Work ledger entries (create if absent):
- `WORK-<nnn>` — Project seeded (type: Decision, status: done).
- `WORK-<nnn>` — Project specification approved (type: Decision, status: done, source references spec ID + version).
- `WORK-<nnn>` — Non-executable scaffold created (type: Task, status: done).

Handoff (console output + work ledger):
- Approved spec ID and version.
- Scaffold location: `generated-projects/<project-name>/`.
- Next recommended action: typically wf-task-code / wf-task-noncode for the first implementation unit.
- Any unresolved open questions carried forward.

## Decision Rules
| Rule | Statement |
|------|-----------|
| No spec, no scaffold | No scaffold, AGENTS.md, or skills installed before explicit specification approval. |
| No tech stack questions | wf-bootstrap never asks about languages, frameworks, databases, ORMs, libraries, package managers, cloud providers, hosting platforms, or testing frameworks. |
| Architecture emerges | Calibration questions only; no presupposed architectural patterns. |
| Human owns approval | Explicit approval required; inferred approval is not sufficient. |
| Non-executable scaffold | Scaffold contains directory structure only; no executable code, tests, or executable configuration. |
| AGENTS.md limits | Project AGENTS.md cannot weaken protected framework invariants. |
| No silent overwrite | Existing customized skills are not silently overwritten. |

## Delegation Strategy
- **Independent review**: Delegated to wf-review or a dedicated reviewer agent. The reviewer must not simply agree with the planner.
- **Architecture calibration**: Handled inline by wf-bootstrap with human decisions; no separate skill required at this stage.
- **Skill installation**: Mechanical copy operation; no delegation needed.

## Approval Gates
| Gate | What | Who |
|------|------|-----|
| Gate 1 | Draft specification independent review | Reviewer agent / wf-review |
| Gate 2 | Explicit project-specification approval | Human |

## Safety Boundaries
- No executable code, tests, or executable configuration before specification approval.
- No technology stack selection or binding at this stage.
- Never silently discard pending work or overwrite customized skills.
- Preserve existing files.
- No force-push (not applicable at bootstrap, noted for consistency with wf-commit).

## Error Handling
| Situation | Handling |
|-----------|----------|
| Spec already exists for project | Warn; do not overwrite; ask user whether to supersede or use existing spec. |
| Approval denied | Stop; no scaffold created; return to discovery. Record as pending work ledger item. |
| Blocking review findings | Return to Step 3; address findings; re-submit for review before approval. |
| Human unavailable for approval | Pause; record as pending Work Ledger `Blocker` item; do not proceed. |
| Skill destination already exists | Warn; ask before overwriting customized project skills. |

## Validation
Before completing, verify:
- Specification file exists at `spec-workspace/projects/<project-name>/specification.md` with `status: Approved`.
- Approval evidence recorded in Work Ledger (`Decision` item referencing spec ID + version).
- Scaffold directories exist at `generated-projects/<project-name>/`; contain no executable code (`.gitkeep` or empty only).
- `AGENTS.md` exists at `generated-projects/<project-name>/AGENTS.md`.
- `binding.yaml` exists at `generated-projects/<project-name>/binding.yaml` referencing the approved spec ID + version.
- `.opencode/skills/` contains project-level skills.
- Handoff recorded with approved spec ID, scaffold location, and next recommended action.

## Handoff Requirements
- Approved spec ID and version.
- Scaffold location (`generated-projects/<project-name>/`).
- Next recommended action (typically wf-task-code / wf-task-noncode for the first implementation unit).
- Work ledger updated with project state (project seeded, spec approved, scaffold created).
- Any unresolved open questions carried forward.

## Related Skills
- **wf-state** — follows bootstrap to discover ongoing project state.
- **wf-reconcile** — evaluates consistency of project specifications.
- **wf-task-code / wf-task-noncode** — next step after bootstrap for individual implementation units.
- **wf-review** — used at Gate 1 for specification review.
- **wf-commit** — for committing the scaffold and specification.
