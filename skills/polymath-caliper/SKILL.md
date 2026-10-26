---
name: polymath-caliper
description: >-
  Bootstraps and runs the Goal-driven planning workflow (init, roadmap, daily,
  weekly, evaluate). Trigger on `/polymath-caliper`, `/polymath-caliper <mode>`,
  or plain requests to init/roadmap/daily/weekly/evaluate. Every mode is
  self-contained: `/polymath-caliper init`, `/polymath-caliper roadmap`,
  `/polymath-caliper daily`, `/polymath-caliper weekly`, and
  `/polymath-caliper evaluate` are complete invocations — the skill owns reads,
  missing-input questions, return shape, and approval gates for each mode.
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
5. **Short invocation is enough for every mode.** Treat `/polymath-caliper <mode>` (or `/polymath-caliper`, or `Use the polymath-caliper skill in <mode> mode`) as a complete request. Never ask the user to paste a longer prompt. Each mode below defines: (1) auto-read, (2) ask only when missing, (3) proposal / output shape, and (4) approvals to request.

## Operation modes

### `init`

Bootstrap or refresh the personal instance in the active workspace using **sequential file gates**, then propose external workspace provisioning. **Invocation:** `/polymath-caliper init` (or automatic fallback when pre-flight finds missing/incomplete personal files) is a complete request — run the procedure below without asking for a longer prompt. Load [workflows.md](references/workflows.md) sections 0–1, [notion-workspace.md](references/notion-workspace.md), [todoist-workspace.md](references/todoist-workspace.md), and [obsidian-workspace.md](references/obsidian-workspace.md) as needed.

#### 1. Auto-read (always)

1. Active workspace `.gitignore` (or `.git/info/exclude`) — ensure `personal/` is ignored before creating or writing personal files; create `personal/` and `personal/focus-areas/` if missing.
2. Existing `personal/goal.md`, `personal/focus-areas/*.md`, `personal/workspaces.md`, `personal/baseline.md` (if any).
3. Matching templates in `templates/` ([goal.example.md](templates/goal.example.md), [focus-area.example.md](templates/focus-area.example.md), [workspaces.example.md](templates/workspaces.example.md), [baseline.example.md](templates/baseline.example.md)).
4. Only at Gate 3 / provisioning: inspect available Notion, Todoist, and local Obsidian paths if the user wants to bind existing resources.

Locate the **first incomplete gate** and start there:

| Gate | Target | Complete when | Who fills |
|---|---|---|---|
| 1 | `personal/goal.md` from [goal.example.md](templates/goal.example.md) | No unreplaced `<PLACEHOLDER>` tokens | User |
| 2 | Each `personal/focus-areas/<slug>.md` listed in the Goal index, from [focus-area.example.md](templates/focus-area.example.md) — one Focus Area file at a time | No unreplaced `<PLACEHOLDER>` tokens | User |
| 3 | `personal/workspaces.md` from [workspaces.example.md](templates/workspaces.example.md) | Init-required fields filled; deferred IDs/URLs may remain placeholders until after approved external setup | User (+ later Planner write-back) |
| 4 | `personal/baseline.md` from [baseline.example.md](templates/baseline.example.md) | No unreplaced placeholders; distance to the Goal and next-task starting points recorded | Planner (may ask short clarifying questions about evidence) |

#### 2. Ask only when missing (current gate only)

Complete one gate before starting the next. Do not interview ahead for later gates. If the user volunteers data for a later file early, stash it and apply it when that gate starts — still validate that gate before moving on.

Accept any input style: **guided interview**, **free-text brain dump**, **existing workspace binding** (Gate 3), or **manual edit**.

For the current gate only:

1. Report what is already filled vs what `<PLACEHOLDER>` fields are missing (or say the file does not exist yet).
2. Ask only for the missing fields of **this** file.
3. When complete, write/update that local `personal/*.md` file, confirm completion, and advance to the next gate.
4. At Gate 4, ask only the minimum evidence questions needed to draft `personal/baseline.md`, then write it.

#### 3. Produce the init provisioning proposal (after Gate 4)

Once Gates 1–4 are complete, return **one** provisioning proposal covering:

1. **Personal files status** — confirm Gates 1–4 complete and `personal/` gitignored
2. **Baseline summary** — overall distance to The Goal and per-Focus-Area starting points
3. **Exact external setup awaiting approval**:
   - **Notion:** create or bind the locked roadmap page (`page_title`) + inline `Roadmap Items` database + seeded Focus Areas and Open Decisions (or bind existing `canonical_page_url`)
   - **Todoist:** create or bind the locked project (`project_name`) + five standard sections (`Inbox`, `This Week`, `Today`, `Waiting / Follow-up`, `Completed Evidence`) + recurring weekly-review task
   - **Obsidian:** verify registered vault paths (read-only; no writes unless explicitly requested)
   - **Write-back:** exact URLs and IDs that will be written into `personal/workspaces.md`

Do **not** create or modify Notion pages, Todoist projects/sections/tasks, or Obsidian notes until step 4 is approved.

#### 4. Approvals to request

End with an explicit approval ask:

- **Approve external provisioning** — create/bind the listed Notion and Todoist resources and write resulting URLs/IDs into `personal/workspaces.md`
- **Approve with edits** — user adjusts names, existing URLs/IDs, or skips one integration; then apply only the revised set
- **Local files only** — keep `personal/*.md` as written; skip external provisioning for now

On approval: create/bind only the approved resources, write resulting URLs and IDs back into `personal/workspaces.md`, and report completed vs skipped steps (or return a ready-to-apply setup checklist if integrations are unavailable). Then suggest `/polymath-caliper roadmap` for the first planning session.

### `roadmap`

Initial or major replanning session. **Invocation:** `/polymath-caliper roadmap` (or equivalent short form) is a complete request — run the full procedure below without asking for a longer prompt. Load [planning-spec.md](references/planning-spec.md), [workflows.md](references/workflows.md) sections 2–4, [notion-workspace.md](references/notion-workspace.md), [todoist-workspace.md](references/todoist-workspace.md), and [obsidian-workspace.md](references/obsidian-workspace.md) as needed.

#### 1. Auto-read (always)

Read in this order, then proceed:

1. `personal/goal.md`, `personal/focus-areas/`, `personal/baseline.md`, `personal/workspaces.md`
2. Locked Notion roadmap page (`canonical_page_url` / `page_title`): current lanes, `Roadmap Items`, Decisions, Weekly plan (when accessible)
3. Locked Todoist project (`project_id` / `project_name`): current open tasks in This Week / Today / Waiting (when accessible)
4. Relevant registered Obsidian vaults: recent notes tied to active Focus Areas (read-only, when useful)

If an integration is unavailable, note it briefly and continue.

#### 2. Ask only when missing

Skip any question already answered in `personal/*.md` or Notion Decisions. Ask only what is still unresolved to:

| Gap | Ask for |
|---|---|
| Baseline stale or incomplete | Current verified evidence and how near/far from The Goal and each Focus Area |
| Deadline or capacity unclear | Exact target deadline, planned execution budget, unplanned reserve, and capacity-limiting constraints |
| Goal-shaped constraints unclear | Hard bounds on the target outcome (role, domain, language, deliverable bar) |
| Open Decisions unresolved | Choices listed under Open Decisions in `personal/goal.md` or Notion Decisions |
| Competing Open-lane candidates | Which **single** Focus Area enters the Open lane now (others stay in Parking Lot) |
| Missing evidence definitions | What verifiable proof each active Focus Area must produce by the deadline |
| External calendar constraints | Upcoming travel, exams, deadlines, or low-capacity weeks |

If nothing is missing, skip the interview and go straight to step 3.

#### 3. Produce the roadmap proposal

Return **one** proposal containing:

1. **Updated Baseline draft** (`personal/baseline.md`) — overall + per-area distance and concrete next-task starting points
2. **Lane assignment table** — every Focus Area mapped to `Build`, `Reading`, `Open` (at most one), or `Parking Lot`, with measurable evidence per item
3. **Multi-week roadmap** — sequenced milestones from now to the Goal deadline, including direct Goal-advancing Build work from week one
4. **First / next week plan** — concrete tasks (25–90 min each) derived from Baseline starting points across Goal/positioning, primary Build, core craft, communication/synthesis, and Open lane; **total ≤ planned execution budget**; unplanned reserve left empty
5. **Exact external changes awaiting approval** — `personal/baseline.md` write, Notion roadmap / `Roadmap Items` / Decisions / Weekly plan updates, and Todoist task creations/updates in `This Week` / `Today`

Do **not** write `personal/baseline.md`, Notion, or Todoist until step 4 is approved.

#### 4. Approvals to request

End with an explicit approval ask:

- **Approve roadmap & week plan** — write `personal/baseline.md` and apply all listed Notion and Todoist changes
- **Approve with edits** — user adjusts lanes, milestones, or tasks; then apply only the revised set
- **Approve roadmap only (hold Todoist)** — update Baseline and Notion roadmap, wait before pushing tasks to Todoist
- **Proposal only** — leave all files and external systems unchanged

On approval: apply only the authorized items inside workspace locks; never delete Todoist or Obsidian objects. Report completed updates, skipped updates, and reasons (or provide a ready-to-apply change set for any unavailable integration).

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
