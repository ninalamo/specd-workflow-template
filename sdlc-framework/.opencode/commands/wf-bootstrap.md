---
name: wf-bootstrap
skill: wf-bootstrap
description: Establish a new project's foundation from a natural-language project description. Produces a technology-agnostic, explicitly approved project specification and a non-executable physical scaffold. Never asks about technology stack. Never writes executable code.
---

# @wf-bootstrap

Invoke the [wf-bootstrap skill](../skills/wf-bootstrap/SKILL.md) to establish a new project's foundation from a natural-language project description.

## Usage

```
@wf-bootstrap <project description>
```

## What it does

1. Discovers business objectives, stakeholders, major capabilities, and workflows
2. Calibrates architecture (composition, repository topology, deployment) via focused questions
3. Drafts a technology-agnostic project specification (`status: Draft`)
4. Routes to independent review (wf-review)
5. Requires explicit human approval before any scaffold generation
6. Creates a non-executable physical scaffold, project AGENTS.md, and installs project-level skills
7. Records initial project state and produces a handoff

## Never

- Asks about technology stack (languages, frameworks, databases, ORMs, libraries, package managers, cloud providers, hosting platforms, testing frameworks)
- Writes executable code, tests, or executable configuration before spec approval
- Presupposes architectural patterns (microservices, Clean Architecture, etc.)

## Related commands

`@wf-state` `@wf-task-code` `@wf-task-noncode` `@wf-review` `@wf-commit`
