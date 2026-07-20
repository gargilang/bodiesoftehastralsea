# Agent Workflow

## Purpose

This workflow coordinates long-running work across OpenCode, Beckett MCP, BlenderMCP, and future agents while keeping decisions, generated work, and validation traceable.

## Task Lifecycle

### 1. Orient

- Read the root `README.md` and `AGENTS.md`.
- Read the relevant concept documents.
- Read applicable technology decisions.
- Read the latest relevant handoff.
- Identify confirmed facts, provisional decisions, assumptions, and unknowns.

### 2. Define

Before implementation, state:

- the concrete outcome
- affected systems and files
- what is outside scope
- environment capabilities required
- whether approval is required
- how the result will be validated

### 3. Inspect

- Examine existing code, scenes, resources, assets, and tests.
- Reuse established patterns where appropriate.
- Do not infer file contents, installed versions, or MCP capabilities.

### 4. Implement

- Make the smallest complete change.
- Keep simulation, engine integration, and presentation responsibilities clear.
- Preserve editable source assets.
- Keep generated output identifiable.
- Avoid unrelated cleanup.
- Stop and ask before crossing a permission boundary.

### 5. Validate

Start narrowly, then broaden only when justified:

- compile or parse the changed code
- run focused tests
- launch the affected scene
- test on Android when platform behavior matters
- inspect generated assets in Blender and Godot
- check performance when changing frame-critical behavior

Report failures truthfully. Do not hide incomplete validation behind a successful unrelated check.

### 6. Document

- Update concept documentation only when an approved design changes.
- Update a decision record when technical direction changes.
- Update environment documentation when a capability is confirmed.
- Create a handoff when work pauses, reaches a milestone, changes agent, or retains important non-obvious context.

## Approval Boundaries

### Normally allowed within an approved task

- read repository files
- edit files within the repository
- create focused tests
- use an already-approved build or test workflow
- run an already-approved Godot project
- use MCP tools within documented capabilities and paths

### Ask first

- install or upgrade software
- use administrator privileges
- alter operating-system or user configuration
- change mirrors, repositories, proxies, shell configuration, terminal configuration, OpenCode configuration, or MCP configuration
- access a new network host or upload project material
- start a server or listener
- add or upgrade a dependency
- delete or overwrite valuable source assets
- make an architectural or language change
- publish an APK
- commit, push, tag, or rewrite Git history

## Source-of-Truth Order

When documents conflict, use this order and report the conflict:

1. explicit current instruction from the project owner
2. accepted decision records
3. current concept documents
4. architecture and convention documents
5. current approved task
6. active handoff
7. code comments
8. archived handoffs

A handoff must never silently override an accepted decision or current concept.

## Checkpoint Guidance

Create a handoff after:

- a meaningful prototype milestone
- a substantial work session with unfinished work
- an architectural change
- a blocked investigation
- a transfer between agents
- a risky migration stage

Do not create handoffs for trivial edits that are fully explained by the diff and permanent documentation.
