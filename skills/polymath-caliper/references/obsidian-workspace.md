# Obsidian Workspace Specification (Knowledge Base)

## Uniform multi-vault model

- A user may maintain one or more independent Obsidian vaults on disk, each covering a specific subject area, domain, or purpose.
- Every registered vault is treated as an equal input to the planning system. No single vault is structurally special or required to hold a master dashboard (Notion is Roadmap & Memory; Todoist is the Task Tracker).
- Runtime vault names, local filesystem paths, optional subfolder scopes, and area mappings live exclusively in `personal/workspaces.md` (at the active workspace root). Never store Obsidian notes inside the repository or hardcode vault names or filesystem paths in tracked process or skill files.
- Map each Focus Area in `personal/focus-areas/<slug>.md` to the vault (and optional subfolder) that holds its notes.

## Strict path isolation and safety boundary

The user's machine and Obsidian vaults may contain unrelated personal or private notes. Every agent operating with this skill must enforce strict path isolation using the registry in `personal/workspaces.md`:

1. **Registered-path lock:**
   - The agent may only read or inspect files inside the local vault paths (and optional scoped subfolders) explicitly listed under `## Obsidian` in `personal/workspaces.md`.
   - If a vault entry specifies a scoped subfolder, the agent must restrict all operations within that vault to that subfolder and its descendants.
   - Never search the broader filesystem for `.md` files or access any Obsidian vault not listed in `personal/workspaces.md`.
   - If `personal/workspaces.md` does not yet list local paths for Obsidian vaults, the agent must ask the user for the paths (or receive pasted notes) before attempting any local file reads.
2. **Read-only default and explicit approval before writes:**
   - Treat all registered Obsidian vaults as **read-only inputs** during planning, daily check-ins, and weekly reviews.
   - During a review or check-in, read only the vault(s) mapped to the Focus Areas or lanes relevant to the current session.
   - Never create or modify notes in any Obsidian vault unless the user explicitly asks for and approves the exact file path and content changes, as required by [SKILL.md](../SKILL.md) and [planning-spec.md](planning-spec.md).
   - Never delete notes, attachments, or folders in any Obsidian vault.

## Vault registry format

In `personal/workspaces.md`, register each vault in a flat table under `## Obsidian`:

| Vault name | Local path | Scoped subfolder (optional) | Areas / topics covered |
|---|---|---|---|
| `<VAULT_NAME_1>` | `<ABSOLUTE_LOCAL_PATH_1>` | `<OPTIONAL_SUBFOLDER>` | `<FOCUS_AREAS_OR_TOPICS>` |
| `<VAULT_NAME_2>` | `<ABSOLUTE_LOCAL_PATH_2>` | `<OPTIONAL_SUBFOLDER>` | `<FOCUS_AREAS_OR_TOPICS>` |

## Study note template and evidence conventions

When taking study or project notes tied to the active Goal inside any registered vault, use this structure so the **Planner** can extract completed learning, blockers, and verifiable evidence during reviews and Baseline updates:

```markdown
# [Topic]

Date:
Source:
Why this matters for my Goal:

## What I learned

## What I built or tested

## Questions / gaps

## Evidence to keep
- Link to code or artifact:
- Link to issue, PR, or repository:
- Story or proof this supports:

## Next action
```
