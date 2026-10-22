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
2. Resolve `personal/` relative to the active workspace root. Check whether `personal/goal.md`, at least one file under `personal/focus-areas/`, `personal/baseline.md`, and `personal/workspaces.md` exist. Treat a file as incomplete if it has unreplaced `<PLACEHOLDER>` tokens that block its gate (for `personal/workspaces.md`, only **init-required** fields block; deferred IDs/URLs may remain placeholders — see [workspaces.example.md](templates/workspaces.example.md)).
3. If any required file is missing or incomplete, or the user requested **`init`**, run **`init`** before any other mode (resume at the first incomplete gate).
4. Otherwise select the requested mode: `init` | `roadmap` | `daily` | `weekly` | `evaluate`. If the user did not name a mode, infer it from the request (bootstrap → `init`, first/multi-week plan → `roadmap`, end-of-day → `daily`, Sunday/weekly review → `weekly`, new interest/course → `evaluate`).

## Operation modes

### `init`

Bootstrap or refresh the personal instance in the active workspace using **sequential file gates**. Complete one gate before starting the next. Do not interview ahead for later gates. If the user volunteers data for a later file early, stash it and apply it when that gate starts—still validate that gate before moving on.

Ensure `personal/` is listed in the active workspace's `.gitignore` (or `.git/info/exclude`) so personal data is never committed. Create `personal/` if needed.

**Input styles** (any gate that needs user data may use one or more):

- **Guided interview** — structured questions for the current file's missing fields only.
- **Free-text brain dump** — parse a pasted dump; apply what belongs to the current gate; ask only for remaining gaps on that file.
- **Existing workspace binding** — for Gate 3, locate or accept an existing Notion page, Todoist project, and/or Obsidian vault paths.
- **Manual edit** — validate a user-edited file against its template; report and ask for any remaining gaps.

**Per-gate behavior** (for the current gate only):

1. Load the matching template from `templates/`.
2. If the target file is missing, say so and start from the template.
3. If it exists, scan for unreplaced `<PLACEHOLDER>` tokens (for Gate 3, only **init-required** placeholders block; see [workspaces.example.md](templates/workspaces.example.md)).
4. Report what is already filled vs what is missing.
5. Ask only for the missing fields for this file (or extract them from a prior brain dump, then ask only for remaining gaps).
6. Write/update that file when complete; confirm completion to the user; then advance.

| Gate | Target | Complete when | Who fills |
|---|---|---|---|
| 1 | `personal/goal.md` from [goal.example.md](templates/goal.example.md) | No unreplaced `<PLACEHOLDER>` tokens | User |
| 2 | Each `personal/focus-areas/<slug>.md` listed in the Goal index, from [focus-area.example.md](templates/focus-area.example.md) — one Focus Area file at a time | No unreplaced `<PLACEHOLDER>` tokens | User |
| 3 | `personal/workspaces.md` from [workspaces.example.md](templates/workspaces.example.md) | Init-required fields filled; deferred IDs/URLs may remain placeholders until after approved external setup | User (+ later Planner write-back) |
| 4 | `personal/baseline.md` from [baseline.example.md](templates/baseline.example.md) | No unreplaced placeholders; distance to the Goal and next-task starting points recorded | Planner (may ask short clarifying questions about evidence) |

After Gate 4: propose Notion page + `Roadmap Items` database and Todoist (Task Tracker) project + sections + recurring weekly-review task per [workflows.md](references/workflows.md), [notion-workspace.md](references/notion-workspace.md), and [todoist-workspace.md](references/todoist-workspace.md). Wait for explicit approval before any external create/update. After approval, write resulting URLs and IDs back into `personal/workspaces.md`. Never create external resources until the user approves.

### `roadmap`

Initial or major replanning session. Read [planning-spec.md](references/planning-spec.md) and [workflows.md](references/workflows.md) sections 2–4 for deeper detail.

1. **Pre-read:** `personal/goal.md`, `personal/focus-areas/`, `personal/baseline.md`, `personal/workspaces.md`, then the locked Notion roadmap page (and the locked Task Tracker project / relevant registered Obsidian vaults when available).
2. **Interview first** — ask only questions needed to:
   - refresh the Baseline (how near or far from The Goal and each Focus Area)
   - confirm the exact deadline and capacity-limiting constraints
   - confirm any goal-shaped constraints that bound the outcome
   - choose the first Open-lane Focus Area if decisions remain unresolved
   - decide what evidence The Goal needs by the deadline
   - identify any external constraints
   - resolve any Open Decisions listed in `personal/goal.md`
3. **After answers:**
   - update `personal/baseline.md`
   - propose a multi-week roadmap
   - assign each Focus Area to one lane
   - define measurable evidence for each major item
   - propose the first/next week using no more than the planned execution budget, with tasks derived from Baseline starting points
   - leave the unplanned reserve empty
   - do not update Notion or Todoist until approved

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
