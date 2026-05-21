---
name: to-spec
description: "Generate a feature spec from the current conversation context. Supports multiple formats: kiro (default, 3-file), spec-kit (rich multi-file). Use when user wants to spec out a feature, create a requirements doc, plan implementation, or mentions /to-spec."
---

# To Spec

Generate a structured feature specification from the current conversation context and codebase understanding.

## Usage

```
/to-spec <slug> [format]
```

- `slug` — kebab-case feature name (becomes the directory name)
- `format` — `kiro` (default) or `spec-kit`

## Output

All specs land in `docs/specs/<slug>/`. No sequential numbers — slug is the unique key.

### Format: `kiro` (default)

```
docs/specs/<slug>/
├── requirements.md    ← user stories + acceptance criteria (WHAT, not HOW)
├── design.md          ← architecture, API contracts, data models, tech choices
└── tasks.md           ← ordered implementation checklist
```

### Format: `spec-kit`

```
docs/specs/<slug>/
├── spec.md            ← feature requirements, user stories, acceptance criteria
├── plan.md            ← technical implementation plan with rationale
├── data-model.md      ← entity schemas and relationships
├── contracts/         ← API contracts (REST endpoints, event schemas, etc.)
│   └── *.md
├── research.md        ← library comparisons, tech decisions research
└── tasks.md           ← executable task list with [P] parallel markers
```

## Process

### 1. Gather context

Work from whatever is already in the conversation. Do NOT re-interview the user — synthesize what you know. If you haven't explored the codebase yet, do so now.

Use the project's domain vocabulary (from `CONTEXT.md` if it exists) and respect existing ADRs.

### 2. Draft requirements

Write the requirements/spec first. Focus on:

- **WHAT** users need and **WHY** — not HOW to implement
- User stories in `As a <actor>, I want <feature>, so that <benefit>` format
- Acceptance criteria as checkboxes
- Mark ambiguities with `[NEEDS CLARIFICATION: specific question]` — don't guess

Present to the user for review before proceeding.

### 3. Draft design / plan

After requirements are approved, write the design:

- Architecture decisions with rationale
- API contracts and data models
- Integration points and dependencies
- Non-functional requirements (performance, security, etc.)

Do NOT include specific file paths or raw code dumps. Exception: if a prototype produced a snippet that encodes a decision precisely (state machine, schema, type shape), inline the decision-rich parts only.

Present to the user for review before proceeding.

### 4. Generate tasks

Break the design into an ordered implementation checklist:

- Tasks are ordered by dependency (do first things first)
- Each task is a thin vertical slice where possible
- Mark independent tasks with `[P]` (can be parallelized)
- Each task has clear done criteria

### 5. Write files

Create the `docs/specs/<slug>/` directory and write all files.

If `docs/specs/` doesn't exist yet, create it.

## Templates

### Kiro: requirements.md

```markdown
# Requirements: <Feature Name>

## Problem Statement

<What problem does this solve, from the user's perspective?>

## User Stories

1. As a <actor>, I want <feature>, so that <benefit>
   - [ ] Acceptance criterion 1
   - [ ] Acceptance criterion 2

## Out of Scope

- <What this feature explicitly does NOT cover>

## Open Questions

- [NEEDS CLARIFICATION: <question>]
```

### Kiro: design.md

```markdown
# Design: <Feature Name>

## Architecture

<High-level architecture description>

## API Contracts

<Endpoint/event/interface definitions>

## Data Model

<Entity schemas and relationships>

## Key Decisions

| Decision | Rationale | Alternatives Considered |
|----------|-----------|------------------------|
| ...      | ...       | ...                    |

## Security & Performance

<Non-functional considerations>
```

### Kiro: tasks.md

```markdown
# Tasks: <Feature Name>

## Implementation Order

- [ ] 1. <Task description> — <done criteria>
- [ ] 2. [P] <Task description> — <done criteria>
- [ ] 3. [P] <Task description> — <done criteria>
- [ ] 4. <Task description> (depends on 2, 3) — <done criteria>

## Notes

- [P] = can be parallelized with adjacent [P] tasks
- Tasks are ordered by dependency; execute top-to-bottom unless marked [P]
```

### Spec-kit: spec.md

```markdown
# Spec: <Feature Name>

<!-- Focus on WHAT and WHY. No tech stack, APIs, or code structure. -->

## Problem Statement

<What problem does this solve?>

## User Stories

1. As a <actor>, I want <feature>, so that <benefit>
   - Acceptance: <measurable criterion>

## Non-Functional Requirements

- Performance: <constraints>
- Security: <constraints>
- Compliance: <constraints>

## Out of Scope

- <Explicit exclusions>

## Checklist

- [ ] No [NEEDS CLARIFICATION] markers remain
- [ ] Requirements are testable and unambiguous
- [ ] Success criteria are measurable
```

### Spec-kit: plan.md

```markdown
# Implementation Plan: <Feature Name>

## Pre-Implementation Gates

- [ ] Simplicity: No over-engineering, no speculative features
- [ ] Integration-first: Contracts defined before implementation
- [ ] Test-first: Test files created before source files

## Architecture

<High-level system changes>

## Phases

### Phase 1: <Name>

**Prerequisites**: None
**Deliverables**: <What's done when this phase completes>

### Phase 2: <Name>

**Prerequisites**: Phase 1
**Deliverables**: <...>

## File Creation Order

1. Contracts (API specs)
2. Test files (contract → integration → e2e → unit)
3. Source files (make tests pass)

## Complexity Tracking

| Added Complexity | Justification |
|-----------------|---------------|
| ...             | ...           |
```

## Conflict-Free by Design

- **No sequential numbers** — the slug IS the identifier
- **Two people, same feature** — they're on the same branch, editing the same spec directory
- **Two people, different features** — different slugs, no conflict possible
- **Ordering** — use frontmatter `date:` field if chronological ordering is needed downstream
