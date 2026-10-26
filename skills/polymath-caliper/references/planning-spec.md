# Planning System Specification

## Outcome

The spine for this instance is defined in `personal/goal.md` (at the active workspace root): target outcome, constraints, capacity, and target date. Focus Areas that serve the Goal live in `personal/focus-areas/`. The Planner evaluates both to produce `personal/baseline.md`.

Plan and evaluate progress against that Goal. Do not invent a different outcome.

## Terms and systems

| Term | Meaning |
|---|---|
| Planner | The decision-making and planning role that reads this skill's instructions, evaluates the Goal and Focus Areas, generates/updates the Baseline, maintains the Roadmap, and proposes daily/weekly tasks for the Task Tracker. |
| The Goal | The single spine (target outcome, deadline, constraints, capacity) in `personal/goal.md` that gives sense to all Focus Areas. |
| Focus Areas | Multiple areas in `personal/focus-areas/*.md` that help reach The Goal, each decomposed into weekly milestones and small incremental tasks. |
| Baseline | Planner-created evaluation in `personal/baseline.md` estimating how near or far we are from The Goal and each Focus Area; used as the starting point to define the next tasks to be planned. |
| Roadmap & Memory | The Notion page and its `Roadmap Items` database bound in `personal/workspaces.md` — long-term plan, weekly milestones, progress log, and decision history. |
| Knowledge Base | Linked study and project notes across the Obsidian vaults registered in `personal/workspaces.md`. |
| Task Tracker | The Todoist project for daily and weekly execution (`tasks out` from the Planner, `progress back` via completions), bound in `personal/workspaces.md`. |
| Evidence | A verifiable output such as a repository, code sample, write-up, architecture note, case study, deliverable, or completed learning artefact. |

## Process vs. personal data separation

This specification and all files in the `polymath-caliper` skill package (`SKILL.md`, `references/**`, `templates/**`) define the reusable, domain-agnostic process only.

1. Never write personal outcomes, constraints, deadlines, specific courses or topics, local filesystem paths, Obsidian vault names/paths, Notion URLs, or Todoist IDs into tracked files or skill files.
2. Keep all instance-specific data and external bindings exclusively in the gitignored `personal/goal.md`, `personal/focus-areas/*.md`, `personal/baseline.md`, and `personal/workspaces.md` files in the active workspace.
3. When evolving the process or changing the schema of `personal/*.md`, update [goal.example.md](../templates/goal.example.md), [focus-area.example.md](../templates/focus-area.example.md), [baseline.example.md](../templates/baseline.example.md), and [workspaces.example.md](../templates/workspaces.example.md) using generic `<PLACEHOLDER>` tokens only.

## Resource hierarchy and incremental decomposition

1. **The Goal (1)** — singular spine in `personal/goal.md`.
2. **Focus Areas (1..N)** — each area in `personal/focus-areas/<slug>.md` exists to advance The Goal.
3. **Baseline** — the Planner evaluates The Goal and Focus Areas against verified evidence and writes `personal/baseline.md` (overall and per-area distance; next-task starting points).
4. **Weekly milestones** — checkpoints inside each active Focus Area, tracked in Roadmap & Memory (Notion).
5. **Incremental tasks (25–90 min)** — concrete units scheduled in the Task Tracker and tracked daily or weekly.

The Planner always uses the current Baseline as the starting point when proposing the next tasks.

## Capacity and lanes

Capacity defaults come from `personal/goal.md`. Section 1 holds goal-shaped constraints (what defines or bounds the outcome). Section 2 holds the target deadline, schedule/budget, and capacity-limiting constraints (time zone, hard availability limits, life/work limits on executable capacity). Typical defaults:

- Work is available on defined weekdays for a fixed daily window.
- A weekly plan may schedule at most the **planned execution budget** (commonly 20 hours, or four hours a day across five weekdays).
- Keep the **unplanned reserve** (commonly 10 hours a week) free for external opportunities, urgent follow-ups, life administration, spillover, and recovery. Do not create scheduled tasks for this reserve.

Use four lanes:

- **Build:** verifiable evidence — projects, deliverables, outreach/positioning materials, and other outputs that directly advance the Goal.
- **Reading:** continuous study that supports the Goal.
- **Open:** one selected exploration topic at a time.
- **Parking Lot:** valuable ideas that have no current commitment.

## Focus Areas and lane mapping

Focus Areas, competing choices, and initial lane assignments live in `personal/goal.md` (index) and `personal/focus-areas/*.md`.

Rules for mapping them:

1. Items that produce immediate verifiable proof toward the Goal belong in **Build**.
2. Continuous supporting study belongs in **Reading**.
3. Competing alternatives (for example two tools, frameworks, or topics) stay in **Parking Lot** until the user chooses one.
4. At most one Parking Lot item may be active in the **Open** lane at a time.
5. Seed unresolved decisions from `personal/goal.md` into the Notion **Decisions** section during setup.

## Planner workflow

### Before planning

1. Read this specification, `personal/goal.md`, `personal/focus-areas/`, `personal/baseline.md` (if it exists), `personal/workspaces.md`, and the relevant current roadmap, notes, and tasks that are accessible.
2. If personal instance files are missing, bootstrap them from [goal.example.md](../templates/goal.example.md), [focus-area.example.md](../templates/focus-area.example.md), [baseline.example.md](../templates/baseline.example.md), and [workspaces.example.md](../templates/workspaces.example.md) via the `init` mode in [SKILL.md](../SKILL.md) before planning.
3. Identify missing inputs, unavailable integrations, and the exact external changes the Planner could make.
4. Ask only for decisions required to create a valid plan.

### Baseline evaluation

When creating or refreshing `personal/baseline.md`, the Planner must:

1. Compare current verified evidence against The Goal's success criteria and each Focus Area's target state.
2. Estimate overall and per-area distance (how near or far we are from The Goal).
3. Record the exact starting point for the next incremental task in each active Focus Area.
4. Use that starting point when proposing Task Tracker items.

### Weekly proposal

Triggered by a short mode invocation (`/polymath-caliper weekly` or equivalent). The Planner auto-reads personal files and locked external systems, asks only for missing inputs, then provides a proposal containing:

1. Completed outcomes and evidence from the previous week.
2. Incomplete work and its actual blockers.
3. Progress and risks against the Goal in `personal/goal.md`, using the current Baseline.
4. Lane changes and any tradeoffs they require.
5. A next-week task list with a clear outcome and 25–90 minute estimate for every task, derived from Baseline starting points and Focus Area milestones.
6. A calculated total within the planned execution budget from `personal/goal.md`.
7. Categories of work that may use the unplanned reserve, without assigning tasks to it.
8. The exact Notion and Todoist (Task Tracker) changes awaiting approval.
9. An updated Baseline draft when distance to the Goal has changed.

Full step order (auto-read → ask-if-missing → proposal → approvals): [SKILL.md](../SKILL.md) `weekly`.

### Approval and updates

End every `init` (external provisioning), `roadmap`, `weekly`, `daily`, and `evaluate` proposal with an explicit approval ask: approve all listed changes, approve with named edits, or keep as proposal/local only (no external writes). Wait for explicit approval. Once approved, apply only the listed roadmap, Baseline, workspace-binding, and task updates that the Planner is authorized and able to perform. Then report the completed updates, skipped updates, and their reasons.

### Daily check-in

Triggered by a short mode invocation (`/polymath-caliper daily` or equivalent). The Planner auto-reads personal files and locked Todoist (Today/This Week), asks only for missing check-in fields, then responds with a short next-day proposal grounded in the current Baseline. Do not reshuffle the full week without approval. End with an explicit approval ask before Todoist/Notion writes.

When asking the user, use this shape (pre-fill what tools already show):

```text
Completed:
Not completed:
Blockers:
New evidence or notes:
Available time tomorrow:
```

Full step order (auto-read → ask-if-missing → proposal → approvals): [SKILL.md](../SKILL.md) `daily`.

## Decision rule

Triggered by a short mode invocation (`/polymath-caliper evaluate` plus the candidate, or equivalent). For a new course, technology, or idea, add it to the Parking Lot (as a Focus Area or candidate) before it receives scheduled work. The Planner auto-reads Goal, Focus Areas, Baseline, and locked Notion lanes, asks only for missing decision inputs, then evaluates:

1. Does it directly improve the chances of achieving the Goal in `personal/goal.md` by the target date?
2. Which active roadmap item or Focus Area would it replace?
3. What evidence would it produce?

Recommend **Reject**, **Park**, or **Promote to Open**. Promote to the Open lane only after explicit approval of that tradeoff. Do not create Task Tracker execution tasks in evaluate. Refresh the Baseline after any approved Focus Area or lane change. Full step order: [SKILL.md](../SKILL.md) `evaluate`.
