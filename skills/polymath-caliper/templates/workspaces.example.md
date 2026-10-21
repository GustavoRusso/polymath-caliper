# Personal Workspace Bindings

Copy this file to `personal/workspaces.md` during tool setup (or ask the Planner `init` mode to create it).

During **`init` Gate 3**, fill **init-required** fields first. **Deferred** fields may keep their `<PLACEHOLDER>` tokens until after approved Notion/Todoist provisioning; the Planner then writes IDs and URLs back here. See [SKILL.md](../SKILL.md) `init`.

`personal/workspaces.md` is gitignored and must not be committed.

Agents must treat the Todoist `project_id` below as the single-project isolation lock for the Task Tracker. Until a `project_id` is recorded, the agent may only create or locate the dedicated project after explicit approval, then lock all subsequent operations to that ID.

## Workspace title

<!-- init-required -->
`<WORKSPACE_TITLE>`

Example: `Focus Plan — <TARGET_DATE>`

## Notion (Roadmap & Memory)

- **Page title:** `<NOTION_PAGE_TITLE>` <!-- init-required -->
- **Canonical page URL:** `<NOTION_PAGE_URL>` <!-- deferred until page exists -->

Leave the URL blank (or keep the placeholder) until the page exists. After creation, paste the share/canonical URL here.

## Todoist (Task Tracker)

- **Project name:** `<TODOIST_PROJECT_NAME>` <!-- init-required -->
- **Project ID:** `<TODOIST_PROJECT_ID>` <!-- deferred until project exists -->

### Sections

Create these sections in this order. Record each section ID after creation. All section IDs below are **deferred** until after approved Todoist setup.

| Order | Section name | Section ID |
|---|---|---|
| 1 | Inbox | `<SECTION_ID_INBOX>` <!-- deferred --> |
| 2 | This Week | `<SECTION_ID_THIS_WEEK>` <!-- deferred --> |
| 3 | Today | `<SECTION_ID_TODAY>` <!-- deferred --> |
| 4 | Waiting / Follow-up | `<SECTION_ID_WAITING>` <!-- deferred --> |
| 5 | Completed Evidence | `<SECTION_ID_COMPLETED_EVIDENCE>` <!-- deferred --> |

### Recurring system trigger task

- **Title:** `Weekly review with your Planner`
- **Task ID:** `<WEEKLY_REVIEW_TASK_ID>` <!-- deferred until task exists -->
- **Section:** This Week (`<SECTION_ID_THIS_WEEK>`)
- **Schedule:** every sunday

## Obsidian (Knowledge Base)

Register one or more Obsidian vaults that act as inputs to the planning system. Every vault is treated equally and locked to its listed local path (and optional scoped subfolder). Map vaults to Focus Areas where useful.

**Init-required** when the user has vaults to register: at least one row with vault name and local path. If the user has no Obsidian vaults yet, replace the table rows with an explicit note (for example `None registered`) so the gate is not blocked by unused placeholders. Scoped subfolder and areas/topics may be left empty or noted as none.

| Vault name | Local path | Scoped subfolder (optional) | Areas / topics covered |
|---|---|---|---|
| `<VAULT_NAME_1>` <!-- init-required if registering vaults --> | `<ABSOLUTE_LOCAL_PATH_1>` <!-- init-required if registering vaults --> | `<OPTIONAL_SUBFOLDER>` | `<FOCUS_AREAS_OR_TOPICS>` |
| `<VAULT_NAME_2>` | `<ABSOLUTE_LOCAL_PATH_2>` | `<OPTIONAL_SUBFOLDER>` | `<FOCUS_AREAS_OR_TOPICS>` |
