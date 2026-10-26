---
name: polymath-caliper
description: >-
  Bootstraps and runs the Goal-driven planning workflow (init, roadmap, daily,
  weekly, evaluate). Trigger on `/polymath-caliper`, `/polymath-caliper <mode>`,
  or plain requests to init/roadmap/daily/weekly/evaluate. Short forms
  `/polymath-caliper daily`, `/polymath-caliper weekly`, and
  `/polymath-caliper evaluate` are enough — the skill owns reads,
  missing-input questions, return shape, and approval gates for each mode.
  Also use when cloning or initializing personal/goal.md, personal/focus-areas/,
  personal/baseline.md, and personal/workspaces.md; configuring
  Notion/Todoist/Obsidian; proposing a multi-week roadmap; or evaluating a new
  interest against the Parking Lot decision rule.
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
5. **Short invocation is enough.** Treat `/polymath-caliper`, `/polymath-caliper <mode>`, or `Use the polymath-caliper skill in <mode> mode` as a full run of that mode. Do not ask the user to paste a longer prompt. The mode section below owns what to read, what to ask when inputs are missing, what to return, and which approvals to request.

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

End-of-day check-in. **Invocation:** `/polymath-caliper daily` (or equivalent short form) is a complete request — run the full procedure below without asking for a longer prompt. Load [planning-spec.md](references/planning-spec.md) (daily check-in), [todoist-workspace.md](references/todoist-workspace.md), and [notion-workspace.md](references/notion-workspace.md) / [obsidian-workspace.md](references/obsidian-workspace.md) as needed. Deeper playbook context: [workflows.md](references/workflows.md) section 5.

#### 1. Auto-read (always)

Read in this order, then proceed — do not wait for the user to paste a check-in template:

1. `personal/goal.md`, `personal/focus-areas/`, `personal/baseline.md`, `personal/workspaces.md`
2. Locked Todoist project: tasks in **Today** and **This Week** (completed today, still open, overdue in-project); note due dates and estimates
3. Locked Notion page when useful: current Weekly plan and active Roadmap Items (status / next action)
4. Relevant registered Obsidian vaults (optional): notes created or edited today that map to active Focus Areas — read-only

If an integration is unavailable, say so briefly and continue; ask only for the gaps in step 2.

#### 2. Ask only when missing

Ask the fewest questions needed. Prefer inferring completed/incomplete from Todoist. Use this shape when asking (fill what you already know; leave blanks for the user):

```text
Completed:
Not completed:
Blockers:
New evidence or notes:
Available time tomorrow:
```

| Gap | Ask for |
|---|---|
| Completions unclear | What finished today (if Todoist does not show it) |
| Incomplete items lack reasons | Why each open/overdue Today item stalled |
| Blockers unknown | What is blocking progress (or confirm none) |
| Evidence ambiguous | Links or short description of new evidence/notes from today |
| Tomorrow capacity unknown | Available time tomorrow (hours or window); default to the daily window in `personal/goal.md` if unstated |
| Week reshuffle requested by user | Confirm they want a mid-week replan (otherwise keep the week intact) |

Do not open a full weekly or roadmap interview. If the day reveals a need for major replanning, suggest `weekly` or `roadmap` after finishing this check-in.

#### 3. Produce the daily proposal

Return **one short** proposal grounded in `personal/baseline.md`:

1. **Today's read-back** — completed, not completed (with reasons), blockers, new evidence (brief)
2. **Tomorrow's plan** — concrete tasks with outcomes and 25–90 min estimates, within tomorrow's available time and the week's planned execution budget
3. **Carry-overs** — what moves to tomorrow vs stays in This Week (no silent whole-week reshuffle)
4. **Exact external changes awaiting approval** — Todoist completes/reschedules/moves into Today (and Notion next-action tweaks only if essential); note if Baseline is unchanged (usual for daily)

Do **not** reshuffle the full week, rewrite the multi-week roadmap, or write Notion/Todoist/`personal/baseline.md` until step 4 is approved. Baseline writes are rare on daily — only propose them when today's evidence clearly changes distance to The Goal.

#### 4. Approvals to request

End with an explicit approval ask:

- **Approve tomorrow's adjustments** — apply the listed Todoist (and any listed Notion) changes
- **Approve with edits** — user names exclusions or modifications; then apply only the revised set
- **Proposal only** — leave external systems unchanged

On approval: apply only approved in-project Todoist updates; stay inside workspace locks; never delete Todoist or Obsidian objects. Report completed vs skipped updates. If access is missing, return a ready-to-apply change set.

### `weekly`

Sunday (or recurring) weekly review. **Invocation:** `/polymath-caliper weekly` (or equivalent short form) is a complete request — run the full procedure below without asking for a longer prompt. Load [planning-spec.md](references/planning-spec.md) (weekly proposal + approval), [todoist-workspace.md](references/todoist-workspace.md), [notion-workspace.md](references/notion-workspace.md), and [obsidian-workspace.md](references/obsidian-workspace.md) as needed. Deeper playbook context: [workflows.md](references/workflows.md) section 6.

#### 1. Auto-read (always)

Read in this order, then proceed — do not wait for the user to list these:

1. `personal/goal.md`, `personal/focus-areas/`, `personal/baseline.md`, `personal/workspaces.md`
2. Locked Todoist project (`project_id` / `project_name` in `personal/workspaces.md`): completed and incomplete tasks for the review window (prefer last 7 days / current week; include This Week, Today, Waiting / Follow-up, Completed Evidence)
3. Locked Notion page (`canonical_page_url` / `page_title`): roadmap lanes, Weekly plan, Decisions, Evidence portfolio, and recent Weekly reviews
4. Relevant registered Obsidian vaults (paths in `personal/workspaces.md`): notes touched or created in the review window that map to active Focus Areas — read-only

If an integration is unavailable, say so briefly and continue with whatever is readable; ask the user only for the gaps listed in step 2.

#### 2. Ask only when missing

Ask the fewest questions needed. Skip any item you already resolved from tools or files.

| Gap | Ask for |
|---|---|
| Review window unclear | Confirm start/end dates (default: last 7 days ending today) |
| Todoist unreachable or empty | Completed outcomes, incomplete tasks, and real reasons/blockers for the week |
| Obsidian unreachable or no notes found | Links or paste of evidence / notes from the week (optional if Todoist + Notion suffice) |
| Incomplete tasks lack reasons | Why each incomplete item stalled (blocker, capacity, priority change, other) |
| Open-lane decision needed | Keep current Open Focus Area, move to Reading, or return to Parking Lot |
| Capacity / deadline changed | Updated planned execution budget, unplanned reserve, or Goal deadline |
| Ambiguous evidence | Which artifacts count as verified progress toward The Goal this week |

Do not interview for a full replanning session; if the user needs major roadmap rebuild, tell them to run `roadmap` instead after this review.

#### 3. Produce the weekly proposal

Return **one** proposal with these sections (same contract as [planning-spec.md](references/planning-spec.md) weekly proposal):

1. **Completed outcomes and evidence** — what finished and what proof exists
2. **Incomplete work** — each item with the real reason/blocker
3. **Progress toward the Goal deadline** — updated Baseline draft (overall + per active Focus Area; next-task starting points)
4. **Risks to the deadline**
5. **Proposed lane changes** — including Open-lane keep / Reading / Parking Lot, with tradeoffs
6. **Next week plan** — tasks with clear outcomes and 25–90 min estimates, derived from Baseline starting points; **total ≤ planned execution budget** from `personal/goal.md`
7. **Unplanned reserve** — categories only; no scheduled tasks in the reserve
8. **Exact external changes awaiting approval** — bullet list of Notion updates, Todoist creates/updates/completes/reschedules/moves, and whether `personal/baseline.md` will be overwritten

Do **not** write Notion, Todoist, Obsidian, or `personal/baseline.md` until step 4 is approved.

#### 4. Approvals to request

End the proposal with an explicit approval ask. Present choices clearly, for example:

- **Approve all listed changes** — write Baseline + apply every Notion/Todoist item listed
- **Approve with edits** — user names exclusions or modifications; then apply only the revised set
- **Proposal only** — leave external systems and Baseline unchanged

On approval: apply only the approved items the Planner can access; refresh `personal/baseline.md` when included; never delete Todoist or Obsidian objects; stay inside workspace locks. Then report completed updates, skipped updates, and reasons. If access is missing, return a ready-to-apply change set instead of claiming updates ran.

### `evaluate`

Parking Lot decision rule for a new course, technology, or idea. **Invocation:** `/polymath-caliper evaluate` plus a short description of the interest (or equivalent short form) is a complete request — run the full procedure below without asking for a longer prompt. Load [planning-spec.md](references/planning-spec.md) (decision rule), [notion-workspace.md](references/notion-workspace.md), and [todoist-workspace.md](references/todoist-workspace.md) as needed. Deeper playbook context: [workflows.md](references/workflows.md) section 7.

#### 1. Auto-read (always)

Read in this order, then proceed:

1. `personal/goal.md`, `personal/focus-areas/`, `personal/baseline.md`, `personal/workspaces.md`
2. Locked Notion page: lanes (especially **Open** and **Parking Lot**), Roadmap Items, Decisions
3. Locked Todoist project (light touch): current This Week load, so replacement cost is visible
4. The candidate itself from the user message (name, link, scope). If only `/polymath-caliper evaluate` was sent with no candidate, ask for that one item first, then continue.

Do not schedule Task Tracker work for the candidate during this mode.

#### 2. Ask only when missing

Ask the fewest questions needed to apply the decision rule.

| Gap | Ask for |
|---|---|
| Candidate unclear | What to evaluate (name, link, one-line scope) |
| Deadline impact unclear | How the user believes it improves chances of hitting The Goal by the deadline (or confirm they are unsure) |
| Replacement unclear | Which active Focus Area or roadmap item it would replace or defer (required before any Open promotion) |
| Evidence unclear | What verifiable evidence it would produce, and by when |
| Open lane already occupied | Confirm displace current Open item, wait in Parking Lot, or reject |
| Capacity conflict | Whether it fits inside the planned execution budget without stealing Build/Reading time needed for the deadline |

#### 3. Produce the evaluate proposal

Return **one** proposal with:

1. **Candidate summary** — what it is and how it maps to The Goal
2. **Decision-rule answers** — (a) improves deadline odds? (b) what it replaces? (c) what evidence it produces?
3. **Recommendation** — one of: **Reject**, **Park** (Parking Lot only), or **Promote to Open** (with explicit displace/replace plan)
4. **Tradeoffs and risks** — capacity, Focus Area contention, deadline risk
5. **Exact external changes awaiting approval** — e.g. add/update Notion Parking Lot or Open item, Decision log entry, optional Focus Area file stub, Baseline refresh if promotion changes distance/starting points; **no** Todoist tasks unless the user later runs `daily`/`weekly`/`roadmap` after approval

Do **not** promote to Open, write Notion/Todoist, create Focus Area files, or refresh `personal/baseline.md` until step 4 is approved.

#### 4. Approvals to request

End with an explicit approval ask:

- **Approve recommendation** — apply the listed Notion/Baseline (and any Focus Area file) changes for Reject / Park / Promote
- **Approve with edits** — user adjusts lane, replacement target, or wording; then apply only the revised set
- **Proposal only** — leave systems unchanged (candidate stays unrecorded unless already in Parking Lot)

On approval: apply only approved items inside workspace locks; never delete Todoist or Obsidian objects; do not add execution tasks in evaluate. After an approved Focus Area or lane change, refresh `personal/baseline.md`. Report completed vs skipped updates, or a ready-to-apply change set if access is missing.

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
