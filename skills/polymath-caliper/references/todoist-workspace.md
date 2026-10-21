# Todoist Workspace Specification (Task Tracker)

## Connection and MCP configuration

- **Official hosted MCP endpoint:** `https://ai.todoist.net/mcp` (Streamable HTTP with OAuth).
- **Typical agent client configuration files (in the active workspace or user config):**
  - Cursor: `.cursor/mcp.json`
  - VS Code / GitHub Copilot: `.vscode/mcp.json`
  - OpenCode: `opencode.json`

## Strict data isolation and safety boundary

The user's Todoist account may contain personal and unrelated data. Every agent operating with this skill must enforce strict single-project isolation using the bindings in `personal/workspaces.md` (at the active workspace root):

1. **Single-project lock:**
   - The agent may only read or write inside the dedicated Todoist project whose `project_name` and `project_id` are recorded in `personal/workspaces.md`.
   - Never read, list, search, create, or modify tasks, sections, comments, or labels in the global Todoist Inbox or in any other project.
   - When discovering or verifying the project, only inspect project metadata to locate the bound project by name/ID, then scope all task and section calls strictly to that `project_id`.
   - If `personal/workspaces.md` does not yet record a `project_id`, the agent may only create or locate the dedicated project after explicit approval, then write the resulting `project_id` and section/task IDs into `personal/workspaces.md` and lock all subsequent operations to that ID.
2. **Allowed and forbidden operations:**
   - **Allowed (with explicit user approval for writes):** Read project/sections/tasks inside the locked project, create sections, create tasks, update tasks, move tasks between sections inside the project, and complete/reopen tasks.
   - **Forbidden:** Never delete tasks, sections, or projects (`delete_*` tools must never be called). Never modify or delete any resource outside the locked project.
3. **Explicit approval before writes:**
   - As required by [SKILL.md](../SKILL.md) and [planning-spec.md](planning-spec.md), propose the exact Todoist changes and wait for explicit user approval before executing any create, update, move, or completion operation.

## Role

Todoist is the bound implementation of the **Task Tracker**: it holds scheduled daily and weekly execution tasks (`tasks out` from the **Planner**) and records completions, deadlines, and incomplete items (`progress back` to the **Planner**).

## Project structure

Runtime names and IDs live only in `personal/workspaces.md`. Do not hardcode project, section, or task IDs in this specification.

- **Project name:** from `personal/workspaces.md`
- **Project ID:** from `personal/workspaces.md`

### Sections

Create these sections inside the locked project in this exact order. After creation, record each section ID in `personal/workspaces.md`.

| Order | Section Name |
|---|---|
| 1 | `Inbox` |
| 2 | `This Week` |
| 3 | `Today` |
| 4 | `Waiting / Follow-up` |
| 5 | `Completed Evidence` |

## Task conventions

### Execution tasks (planned capacity)

Use this format for all planned execution tasks:

```text
[Verb] [specific outcome] — [time estimate]
```

Rules for execution tasks:

- One clear, verifiable outcome per task.
- A time estimate between **25 and 90 minutes**.
- Derived from Baseline starting points and Focus Area milestones in `personal/baseline.md` and `personal/focus-areas/*.md`.
- Scheduled within the planned execution budget defined in `personal/goal.md` (commonly Monday through Friday, totaling at most 20 hours per week).
- Assign a due date only when the task is genuinely planned for that date.
- Include a link to the source material, Focus Area, or Notion roadmap item in the task description when useful.

### Recurring system trigger task

Create one recurring trigger task in the `This Week` section, then record its task ID in `personal/workspaces.md`:

- **Title:** `Weekly review with your Planner` (or the instance title recorded in `personal/workspaces.md`)
- **Section:** `This Week`
- **Schedule:** `every sunday`
- **Capacity treatment:** This task is a trigger for the user to invoke the Planner's weekly review workflow. The Planner executes the review and prepares the weekly proposal, so this item does not carry a 25–90 minute execution estimate and does not count against the planned execution budget.
