# Notion Workspace Specification (Roadmap & Memory)

## Connection and MCP configuration

- **Official hosted MCP endpoint:** `https://mcp.notion.com/mcp` (Streamable HTTP with OAuth).
- **Typical agent client configuration files (in the active workspace or user config):**
  - Cursor: `.cursor/mcp.json`
  - VS Code / GitHub Copilot: `.vscode/mcp.json`
  - OpenCode: `opencode.json`

## Strict data isolation and safety boundary

The user's Notion workspace may contain unrelated personal pages and databases. Every agent operating with this skill must enforce strict single-page isolation using the binding in `personal/workspaces.md` (at the active workspace root):

1. **Single-page lock:**
   - The agent may only read or write inside the dedicated Notion page (and its inline `Roadmap Items` database) whose `page_title` and `canonical_page_url` are recorded in `personal/workspaces.md`.
   - Never read, search, modify, or delete unrelated pages or databases in the user's Notion workspace.
2. **Explicit approval before writes:**
   - As required by [SKILL.md](../SKILL.md) and [planning-spec.md](planning-spec.md), propose the exact Notion page and database changes and wait for explicit user approval before executing any create or update operation.

## Location and access

- Create one top-level Notion page titled with the workspace title from `personal/workspaces.md`.
- Record the canonical page URL in `personal/workspaces.md` after creation. That file is the runtime source of truth for the page binding.
- Notion is the **Roadmap & Memory** for this system: live roadmap, weekly milestones, progress log, and decision history.
- Populate **Outcome** from `personal/goal.md`, **Baseline Evaluation** from `personal/baseline.md`, initial **Roadmap Items** from `personal/focus-areas/`, and **Decisions** from `personal/goal.md`.
- The **Planner** may read the workspace when it has authorized access.
- The **Planner** may write only the exact changes explicitly approved by the user, as required by [SKILL.md](../SKILL.md).
- A Planner without Notion access must provide ready-to-apply page and database changes instead of claiming an update was made.

## Page content

Create these sections in this order:

1. **Outcome**
   - Target outcome and goal-shaped constraints/context from `personal/goal.md` section 1; target date and capacity-limiting constraints from section 2
2. **Baseline Evaluation**
   - Distance to the Goal, per Focus Area evaluations, and next-task starting points from `personal/baseline.md`
3. **Lanes**
   - Build
   - Reading
   - Open
   - Parking Lot
4. **Weekly Plan**
5. **Weekly Reviews**
6. **Evidence Portfolio**
7. **Decisions**

## `Roadmap Items` database

Create one inline database named **Roadmap Items** with these properties:

| Property | Type | Allowed values / purpose |
|---|---|---|
| Item | Title | Focus Area, goal, skill, project, or deliverable |
| Lane | Select | Build; Reading; Open; Parking Lot |
| Status | Select | Not started; Active; Blocked; Done |
| Outcome value | Select | High; Medium; Low; leave blank until assessed |
| Evidence | URL or text | Verifiable output |
| Next action | Text | One concrete next step (prefer Baseline starting point) |
| Review date | Date | Next date to reconsider the item |

Seed records from the Focus Areas index in `personal/goal.md` and the files under `personal/focus-areas/`. Leave `Outcome value`, `Evidence`, and `Review date` blank unless the Goal or Focus Area already specifies them. Set `Next action` from `personal/baseline.md` when available; otherwise use **Assess during the first roadmap review.**

## Decisions

Add the unresolved decisions listed in `personal/goal.md` to the **Decisions** section. Do not invent additional decisions without asking the user.
