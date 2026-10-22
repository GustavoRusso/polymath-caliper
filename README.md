# Polymath Caliper

A reusable, Planner-assisted planning and execution system for turning broad interests into focused, verifiable progress toward a defined Goal (target outcome, constraints, and deadline).

## Background & Inspiration

This repository implements the methodology described in the article:

- [If You Have Too Many Interests, Build a System](https://multyhead.substack.com/p/if-you-have-too-many-interests-build)

## How It Works

The system coordinates five components with strict separation between the **reusable process** (the self-contained skill at [skills/polymath-caliper/](skills/polymath-caliper/)) and your **personal instance data** (gitignored `personal/`, created on `init`):

1. **Resources (`personal/goal.md`, `personal/focus-areas/`, `personal/baseline.md`):** One **Goal** (spine), many **Focus Areas** that serve it, and a **Baseline** evaluation of how near or far you are from The Goal — used as the starting point for the next tasks.
2. **Planner (`AGENTS.md`, `skills/polymath-caliper/`):** Evaluates The Goal and Focus Areas, writes and refreshes the Baseline, builds the roadmap, splits work into weekly milestones and daily tasks, and runs daily check-ins, weekly reviews, and interest evaluations — always requiring explicit approval before updating any external tool.
3. **Roadmap & Memory / Notion (`skills/polymath-caliper/references/notion-workspace.md`):** Holds the live roadmap (`Build`, `Reading`, `Open`, and `Parking Lot` lanes), Baseline evaluation, weekly reviews, evidence portfolio, and decision log.
4. **Knowledge Base / Obsidian (`skills/polymath-caliper/references/obsidian-workspace.md`):** Connects one or more independent domain/topic vaults as read-only inputs for study and project notes mapped to Focus Areas.
5. **Task Tracker / Todoist (`skills/polymath-caliper/references/todoist-workspace.md`):** Manages daily and weekly execution tasks within a single isolated project (`tasks out` from the Planner, `progress back` via completions), protecting your unplanned reserve capacity.

**Weekly loop:** tick off Task Tracker items → Planner reviews progress → updates the Baseline and Notion roadmap → plans next week.

## Getting Started

Invoke the `polymath-caliper` skill with an operation mode:

| Mode | Purpose |
|---|---|
| `init` | Complete personal files one gate at a time (`goal.md` → each `focus-areas/<slug>.md` → `workspaces.md` → Planner-written `baseline.md`), asking only for missing fields on the current file; then provision Notion/Todoist after approval |
| `roadmap` | Multi-week roadmap and first/next week proposal from the Baseline |
| `daily` | End-of-day check-in and tomorrow's adjustments |
| `weekly` | Weekly review across Task Tracker, Knowledge Base, and Roadmap & Memory; refresh Baseline |
| `evaluate` | Parking Lot trade-off for a new interest or course |

Examples:

```text
Use the polymath-caliper skill in init mode.
Complete personal files one gate at a time; for the current file only,
report what is missing and ask for it.
Do not create Notion or Todoist resources until I approve.
```

```text
Use the polymath-caliper skill in roadmap mode.
Read personal/goal.md, personal/focus-areas/, personal/baseline.md, and personal/workspaces.md first.
Then read my Notion roadmap page.

Interview me before planning. Ask only questions needed to:
1. refresh my Baseline (how near or far I am from The Goal and each Focus Area),
2. confirm my exact deadline and capacity-limiting constraints,
3. confirm any goal-shaped constraints that bound the outcome,
4. choose the first Open-lane Focus Area if decisions remain unresolved,
5. decide what evidence my Goal needs by the deadline,
6. identify any external constraints.

After I answer:
- update personal/baseline.md,
- propose a multi-week roadmap,
- assign each Focus Area to one lane,
- define measurable evidence for each major item,
- propose the first week using no more than my planned execution budget,
  with tasks derived from Baseline starting points,
- leave the unplanned reserve empty,
- do not update Notion or Todoist until I approve.
```

Full playbooks and prompts: [skills/polymath-caliper/references/workflows.md](skills/polymath-caliper/references/workflows.md).

## Redistributing the Skill to Other Projects

The [skills/polymath-caliper/](skills/polymath-caliper/) folder is completely self-contained (`SKILL.md`, `references/`, and `templates/`) and resolves `personal/` relative to the active workspace root:

- **Global (all projects in Cursor):** Copy or symlink `skills/polymath-caliper` to `~/.cursor/skills/polymath-caliper`.
- **Single target project:** Copy `skills/polymath-caliper` to `<project>/.cursor/skills/polymath-caliper`.
