# Personal Workspace Bindings

Copy this file to `personal/workspaces.md` during tool setup (or ask the Planner `init` mode to create it). Fill in titles first; record IDs, URLs, and vault paths after you configure the Notion page (Roadmap & Memory), Todoist project (Task Tracker), and Obsidian vaults (Knowledge Base) — or ask the Planner to set them up with your approval and write the bindings back here.

`personal/workspaces.md` is gitignored and must not be committed.

Agents must treat the Todoist `project_id` below as the single-project isolation lock for the Task Tracker. Until a `project_id` is recorded, the agent may only create or locate the dedicated project after explicit approval, then lock all subsequent operations to that ID.

## Workspace title

`<WORKSPACE_TITLE>`

Example: `Focus Plan — <TARGET_DATE>`

## Notion (Roadmap & Memory)

- **Page title:** `<NOTION_PAGE_TITLE>`
- **Canonical page URL:** `<NOTION_PAGE_URL>`

Leave the URL blank until the page exists. After creation, paste the share/canonical URL here.

## Todoist (Task Tracker)

- **Project name:** `<TODOIST_PROJECT_NAME>`
- **Project ID:** `<TODOIST_PROJECT_ID>`

### Sections

Create these sections in this order. Record each section ID after creation.

| Order | Section name | Section ID |
|---|---|---|
| 1 | Inbox | `<SECTION_ID_INBOX>` |
| 2 | This Week | `<SECTION_ID_THIS_WEEK>` |
| 3 | Today | `<SECTION_ID_TODAY>` |
| 4 | Waiting / Follow-up | `<SECTION_ID_WAITING>` |
| 5 | Completed Evidence | `<SECTION_ID_COMPLETED_EVIDENCE>` |

### Recurring system trigger task

- **Title:** `Weekly review with your Planner`
- **Task ID:** `<WEEKLY_REVIEW_TASK_ID>`
- **Section:** This Week (`<SECTION_ID_THIS_WEEK>`)
- **Schedule:** every sunday

## Obsidian (Knowledge Base)

Register one or more Obsidian vaults that act as inputs to the planning system. Every vault is treated equally and locked to its listed local path (and optional scoped subfolder). Map vaults to Focus Areas where useful.

| Vault name | Local path | Scoped subfolder (optional) | Areas / topics covered |
|---|---|---|---|
| `<VAULT_NAME_1>` | `<ABSOLUTE_LOCAL_PATH_1>` | `<OPTIONAL_SUBFOLDER>` | `<FOCUS_AREAS_OR_TOPICS>` |
| `<VAULT_NAME_2>` | `<ABSOLUTE_LOCAL_PATH_2>` | `<OPTIONAL_SUBFOLDER>` | `<FOCUS_AREAS_OR_TOPICS>` |
