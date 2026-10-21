---
name: polymath-caliper
description: >-
  Bootstraps and runs the Goal-driven planning workflow (init, roadmap, daily,
  weekly, evaluate). Use when cloning or initializing personal/goal.md,
  personal/focus-areas/, personal/baseline.md, and personal/workspaces.md;
  configuring Notion/Todoist/Obsidian for this system; proposing a multi-week
  roadmap; running a daily check-in; running a weekly review; or evaluating a
  new interest against the Parking Lot decision rule.
---

# Polymath Caliper — Goal-Driven Planning

Self-contained, redistributable skill for the polymath-caliper planning system. Process docs and templates live in typed subfolders inside this skill package (`references/` and `templates/`); instance data lives only in gitignored `personal/` at the active workspace root.

```text
polymath-caliper/
├── SKILL.md                 # Entry point, isolation rules, and operation modes
├── templates/               # Init boilerplate for personal/*
└── references/              # Specs and workflow playbooks
```

## Pre-flight

1. Apply the [Isolation and approval](#isolation-and-approval-always) rules below on every invocation. If the active workspace also has an `AGENTS.md`, read it for any additional project constraints.
2. Resolve `personal/` relative to the active workspace root. Check whether `personal/goal.md`, at least one file under `personal/focus-areas/`, `personal/baseline.md`, and `personal/workspaces.md` exist and contain no unreplaced `<PLACEHOLDER>` tokens.
3. If any required file is missing or incomplete, or the user requested **`init`**, run **`init`** before any other mode.
4. Otherwise select the requested mode: `init` | `roadmap` | `daily` | `weekly` | `evaluate`. If the user did not name a mode, infer it from the request (bootstrap → `init`, first/multi-week plan → `roadmap`, end-of-day → `daily`, Sunday/weekly review → `weekly`, new interest/course → `evaluate`).

## Operation modes

### `init`

Bootstrap or refresh the personal instance in the active workspace.

1. Read [goal.example.md](templates/goal.example.md), [focus-area.example.md](templates/focus-area.example.md), [baseline.example.md](templates/baseline.example.md), and [workspaces.example.md](templates/workspaces.example.md).
2. Collect instance data via one of:
   - **Guided interview** — structured questions for The Goal, Focus Areas, capacity, workspace titles, and Obsidian vault paths.
   - **Free-text brain dump** — parse a pasted goals/constraints/schedule dump; ask only for missing fields.
   - **Existing workspace binding** — locate or accept an existing Notion page, Todoist project, and/or Obsidian vault paths; record URLs/IDs/paths in `personal/workspaces.md`.
   - **Manual edit** — validate user-edited `personal/*.md` files against the templates.
3. Ensure `personal/` is listed in the active workspace's `.gitignore` (or `.git/info/exclude`) so personal data is never committed, then write `personal/goal.md`, `personal/focus-areas/<slug>.md` for each Focus Area, and `personal/workspaces.md` (creating `personal/` if needed).
4. As the **Planner**, evaluate The Goal and Focus Areas against available evidence and write `personal/baseline.md` (distance to the Goal; next-task starting points).
5. Propose Notion page + `Roadmap Items` database and Todoist (Task Tracker) project + sections + recurring weekly-review task per [workflows.md](references/workflows.md), [notion-workspace.md](references/notion-workspace.md), and [todoist-workspace.md](references/todoist-workspace.md).
6. Wait for explicit approval before any external create/update. After approval, write resulting URLs and IDs back into `personal/workspaces.md`.
7. Never create external resources until the user approves.

### `roadmap`

Initial or major replanning session. Read [planning-spec.md](references/planning-spec.md) and [workflows.md](references/workflows.md) sections 2–4. Interview only as needed, refresh `personal/baseline.md` from The Goal and Focus Areas, then propose a multi-week roadmap, lane assignments, measurable evidence, and a first/next week within the planned execution budget — using Baseline starting points for Task Tracker tasks. Do not update Notion or Todoist until approved.

### `daily`

End-of-day check-in. Expect (or ask for):

```text
Completed:
Not completed:
Blockers:
New evidence or notes:
Available time tomorrow:
```

Return a short proposal for tomorrow grounded in `personal/baseline.md`. Do not reshuffle the full week without approval. Details: [planning-spec.md](references/planning-spec.md) and [workflows.md](references/workflows.md) section 5.

### `weekly`

Sunday (or recurring) weekly review. Read the locked Todoist (Task Tracker) project, relevant registered Obsidian vaults (Knowledge Base), and the locked Notion page (Roadmap & Memory). Refresh `personal/baseline.md` when distance to the Goal has changed. Return the weekly proposal from [planning-spec.md](references/planning-spec.md). Do not update external systems until approved. Details: [workflows.md](references/workflows.md) section 6.

### `evaluate`

Apply the Parking Lot decision rule to a new course, technology, or idea. Ask whether it improves chances of hitting the Goal deadline, which active Focus Area or item it would replace, and what evidence it would produce. Promote to Open only after explicit approval; then refresh the Baseline. Details: [planning-spec.md](references/planning-spec.md) and [workflows.md](references/workflows.md) section 7.

## Isolation and approval (always)

- Enforce process vs. personal separation: never write personal goals, outcomes, topics, local paths, vault names, URLs, or IDs into tracked files or into this skill package (`SKILL.md`, `references/**`, `templates/**`). Keep all instance data inside gitignored `personal/` at the active workspace root.
- Notion (Roadmap & Memory): single-page lock via `page_title` / `canonical_page_url` in `personal/workspaces.md` — see [notion-workspace.md](references/notion-workspace.md).
- Todoist (Task Tracker): single-project lock via `project_name` / `project_id` — see [todoist-workspace.md](references/todoist-workspace.md). Never delete Todoist objects.
- Obsidian (Knowledge Base): registered-path lock; vaults are read-only by default — see [obsidian-workspace.md](references/obsidian-workspace.md). Never delete notes.
- Discover available integrations before proposing writes. Propose exact changes; apply only after explicit approval; report completed and skipped updates.

## Progressive disclosure

| File | Load when |
|---|---|
| [references/planning-spec.md](references/planning-spec.md) | Any planning, review, or evaluate work |
| [references/workflows.md](references/workflows.md) | Setup, mode playbooks, and example prompts |
| [references/notion-workspace.md](references/notion-workspace.md) | Any Notion read/write |
| [references/obsidian-workspace.md](references/obsidian-workspace.md) | Any Obsidian read/configure |
| [references/todoist-workspace.md](references/todoist-workspace.md) | Any Todoist / Task Tracker read/write |
| [templates/goal.example.md](templates/goal.example.md) | `init` |
| [templates/focus-area.example.md](templates/focus-area.example.md) | `init` |
| [templates/baseline.example.md](templates/baseline.example.md) | `init`, `roadmap`, `weekly` |
| [templates/workspaces.example.md](templates/workspaces.example.md) | `init` |
