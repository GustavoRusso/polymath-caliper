# Planner Protocol

This repository is a reusable planning and execution system. Any agent acting as the **Planner** here must follow this protocol.

## Required reading

Before planning, reviewing progress, or updating an external system:

1. Prefer invoking [skills/polymath-caliper/SKILL.md](skills/polymath-caliper/SKILL.md) with the appropriate mode (`init`, `roadmap`, `daily`, `weekly`, or `evaluate`).
2. Read [skills/polymath-caliper/references/planning-spec.md](skills/polymath-caliper/references/planning-spec.md) — generic process rules.
3. Read [personal/goal.md](personal/goal.md) — this instance's Goal (target outcome, constraints, capacity, and deadline) and Focus Areas index.
4. Read [personal/focus-areas/](personal/focus-areas/) — Focus Area definitions that serve The Goal.
5. Read [personal/baseline.md](personal/baseline.md) — the Planner's evaluation of distance to The Goal and next-task starting points (create or refresh if missing or stale).
6. Read [personal/workspaces.md](personal/workspaces.md) — this instance's Notion, Todoist, and Obsidian bindings.

Read [skills/polymath-caliper/references/workflows.md](skills/polymath-caliper/references/workflows.md) when configuring or changing the Notion, Obsidian, or Todoist workflow.

Read [skills/polymath-caliper/references/notion-workspace.md](skills/polymath-caliper/references/notion-workspace.md) before creating or changing the Notion workspace (Roadmap & Memory).

Read [skills/polymath-caliper/references/obsidian-workspace.md](skills/polymath-caliper/references/obsidian-workspace.md) before reading, configuring, or referencing any Obsidian vault (Knowledge Base).

Read [skills/polymath-caliper/references/todoist-workspace.md](skills/polymath-caliper/references/todoist-workspace.md) before reading, creating, or changing anything in Todoist (Task Tracker).

### Personal instance bootstrap

If `personal/goal.md`, `personal/focus-areas/`, `personal/baseline.md`, or `personal/workspaces.md` does not exist (or still contains unreplaced `<PLACEHOLDER>` tokens that block its gate — for workspaces, only init-required fields block):

1. Run the `polymath-caliper` skill in **`init`** mode. Follow its **sequential file gates**: `personal/goal.md` → each `personal/focus-areas/<slug>.md` → `personal/workspaces.md` → Planner-written `personal/baseline.md`. Templates: [goal.example.md](skills/polymath-caliper/templates/goal.example.md), [focus-area.example.md](skills/polymath-caliper/templates/focus-area.example.md), [workspaces.example.md](skills/polymath-caliper/templates/workspaces.example.md), [baseline.example.md](skills/polymath-caliper/templates/baseline.example.md).
2. For the current gate only: report missing fields, ask for them (or extract from a brain dump / validate a manual edit), write the file when complete, then advance.
3. Do not create Notion pages, Todoist projects, or other external resources until the user explicitly approves those setup steps.
4. After approved external setup, write the resulting URLs, paths, and IDs into `personal/workspaces.md`.

Never commit anything under `personal/`. That directory is gitignored personal instance data created on demand during `init`.

## Source of truth

- [skills/polymath-caliper/](skills/polymath-caliper/) defines the operating rules, tool specs, templates, and workflow playbooks (canonical registry-ready package).
- [.agents/skills/polymath-caliper/SKILL.md](.agents/skills/polymath-caliper/SKILL.md) is an entrypoint shim only (`@skills/polymath-caliper/SKILL.md`); do not put process body or personal data in it.
- `personal/goal.md` defines this instance's Goal (spine), capacity, and Focus Areas index.
- `personal/focus-areas/*.md` define the Focus Areas that serve The Goal.
- `personal/baseline.md` holds the Planner's evaluation of distance to The Goal and the starting point for next tasks.
- `personal/workspaces.md` defines this instance's live external workspace bindings.
- Notion is Roadmap & Memory (live roadmap and decision log).
- Obsidian is the Knowledge Base (personal learning and project notes across the registered vaults).
- Todoist is the Task Tracker (daily and weekly execution tasks).

## Process vs. personal data separation

This repository separates generic process specifications from personal instance data. Any agent modifying the workflow, specifications, templates, or skills in the future must enforce this boundary:

- **Tracked process files (`AGENTS.md`, `skills/polymath-caliper/**`, `.agents/skills/polymath-caliper/**`):**
  - Must remain 100% generic, domain-agnostic, reusable, and free of personal instance data.
  - Edit skill behavior only under `skills/polymath-caliper/`; keep the `.agents` shim as a thin `@` pointer (sync `name` / `description` when the canonical frontmatter changes).
  - Never write personal outcomes, roles, locations, deadlines, specific courses or topics, local filesystem paths, Obsidian vault names/paths, Notion URLs, or Todoist IDs into any tracked file.
  - Use generic angle-bracket placeholders (for example `<TARGET_OUTCOME>`, `<VAULT_NAME>`, `<LOCAL_PATH>`) in all example templates and specifications.
  - Whenever a process change alters the structure of `personal/goal.md`, `personal/focus-areas/*.md`, `personal/baseline.md`, or `personal/workspaces.md`, update [skills/polymath-caliper/templates/goal.example.md](skills/polymath-caliper/templates/goal.example.md), [skills/polymath-caliper/templates/focus-area.example.md](skills/polymath-caliper/templates/focus-area.example.md), [skills/polymath-caliper/templates/baseline.example.md](skills/polymath-caliper/templates/baseline.example.md), and [skills/polymath-caliper/templates/workspaces.example.md](skills/polymath-caliper/templates/workspaces.example.md) using placeholders only, while keeping instance values strictly inside the gitignored `personal/` directory.
- **Gitignored personal files (`personal/**`, `.env*`, `*.local.json`):**
  - Hold all instance-specific Goal details, Focus Areas, Baseline evaluations, local Obsidian vault paths, Notion URLs, and Todoist IDs.
  - Must never be staged or committed to git.

## External-system protocol

- Discover the integrations, permissions, and accessible data before relying on them. Do not assume access to Notion, Obsidian, Todoist, or another external system.
- Propose the exact roadmap and task changes before making external writes.
- Do not add tasks, change the roadmap, or update an external system until the user explicitly approves the proposal.
- Treat approval as authorization only for the proposal and listed external changes. Do not make unrelated changes.
- When the current Planner lacks access to a required system, produce a ready-to-apply change set instead of claiming that the system was updated.
- After an approved update, report the changes made and any changes that could not be completed.

## Notion isolation rules (Roadmap & Memory)

- The user's Notion workspace may contain unrelated personal pages and databases. Agents must strictly isolate all operations to the single page (and its inline `Roadmap Items` database) identified in [personal/workspaces.md](personal/workspaces.md) (`page_title` and `canonical_page_url`).
- Never read, search, modify, or delete pages or databases outside the locked page in `personal/workspaces.md`.

## Todoist isolation rules (Task Tracker)

- The user's Todoist account may contain unrelated personal data. Agents must strictly isolate all operations to the single project named and identified in [personal/workspaces.md](personal/workspaces.md) (`project_name` and `project_id`).
- If `personal/workspaces.md` does not yet record a `project_id`, the agent may only create or locate the dedicated project after explicit user approval, then write the resulting `project_id` (and section/task IDs) into `personal/workspaces.md` and lock all subsequent operations to that ID.
- Never read, search, create, update, or complete tasks in the global Inbox or in any project other than the one locked in `personal/workspaces.md`.
- Never delete tasks, sections, or projects in Todoist. Only read, create, update, move across sections, and complete/reopen tasks within the locked project after explicit user approval.

## Obsidian isolation rules (Knowledge Base)

- The user may work across multiple independent Obsidian vaults on disk, each covering a specific area. Every vault registered under `## Obsidian` in [personal/workspaces.md](personal/workspaces.md) is an equal input to the system.
- Agents must strictly isolate all local note reads to the vault paths (and optional scoped subfolders) registered in `personal/workspaces.md`. Never search the broader filesystem for markdown notes or access unmapped vaults.
- Treat all registered Obsidian vaults as read-only inputs by default. Only create or modify notes inside a registered vault after explicit user approval of the exact target path and content.
- Never delete notes, attachments, or folders in any Obsidian vault.
