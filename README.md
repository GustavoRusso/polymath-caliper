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

Every operation mode is self-contained — invoke it with `/polymath-caliper <mode>` and the skill handles what to read, what to ask when inputs are missing, what to return, and which approvals to request:

| Invocation | Mode | What it does |
|---|---|---|
| `/polymath-caliper init` | `init` | Complete `personal/goal.md` → `personal/focus-areas/<slug>.md` → `personal/workspaces.md` → `personal/baseline.md` one gate at a time; then propose Notion/Todoist provisioning for approval |
| `/polymath-caliper roadmap` | `roadmap` | Auto-read personal + external state, ask only unresolved roadmap questions, propose Baseline refresh + lane map + multi-week roadmap + Week 1 plan for approval |
| `/polymath-caliper daily` | `daily` | End-of-day check-in; auto-read Todoist Today/This Week, ask only for missing check-in fields, propose tomorrow's adjustments for approval |
| `/polymath-caliper weekly` | `weekly` | Weekly review across Todoist, Obsidian, and Notion; draft updated Baseline and next-week plan within budget for approval |
| `/polymath-caliper evaluate` | `evaluate` | Evaluate a new course/idea against the Parking Lot rule (include a one-line description or link, or let the Planner ask for it) |

Full playbooks: [skills/polymath-caliper/references/workflows.md](skills/polymath-caliper/references/workflows.md).

## Redistributing the Skill to Other Projects

The [skills/polymath-caliper/](skills/polymath-caliper/) folder is completely self-contained (`SKILL.md`, `references/`, and `templates/`) and resolves `personal/` relative to the active workspace root:

- **Global (all projects in Cursor):** Copy or symlink `skills/polymath-caliper` to `~/.cursor/skills/polymath-caliper`.
- **Single target project:** Copy `skills/polymath-caliper` to `<project>/.cursor/skills/polymath-caliper`.
