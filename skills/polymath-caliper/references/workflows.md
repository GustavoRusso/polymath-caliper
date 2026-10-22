Your system has a clear Goal defined in `personal/goal.md` (at the active workspace root): **target outcome, constraints, capacity, and deadline**. Focus Areas that serve that Goal live in `personal/focus-areas/`. The **Planner** evaluates both into `personal/baseline.md`, which is the starting point for the next tasks in the **Task Tracker**.

The system schedules only the planned execution budget from The Goal (commonly 20 hours across Monday–Friday), while protecting the unplanned reserve (commonly 10 hours) for external opportunities, urgent follow-ups, unexpected tasks, and recovery.

Invoke the `polymath-caliper` skill with an operation mode (`init`, `roadmap`, `daily`, `weekly`, or `evaluate`). See [SKILL.md](../SKILL.md) for mode definitions and pre-flight checks.

## 0. Initialize the personal instance (`init`)

Do this once when starting a new Goal in any workspace. Invoke the `polymath-caliper` skill with mode `init`.

`init` runs as **sequential file gates**: one file at a time. The Planner reports what is missing on the current file, asks only for those gaps, writes the file when complete, then moves to the next gate. See [SKILL.md](../SKILL.md) `init` for per-gate behavior.

Templates live in [templates/](../templates/). Order:

| Gate | Copy from | Write to | Complete when |
|---|---|---|---|
| 1 | [goal.example.md](../templates/goal.example.md) | `personal/goal.md` | No unreplaced `<PLACEHOLDER>` tokens |
| 2 | [focus-area.example.md](../templates/focus-area.example.md) | `personal/focus-areas/<slug>.md` for each Focus Area in the Goal index (one file at a time) | No unreplaced `<PLACEHOLDER>` tokens |
| 3 | [workspaces.example.md](../templates/workspaces.example.md) | `personal/workspaces.md` | Init-required fields filled; deferred IDs/URLs may stay placeholders until after approved external setup |
| 4 | [baseline.example.md](../templates/baseline.example.md) | `personal/baseline.md` (Planner writes) | No unreplaced placeholders |

Input styles for Gates 1–3:

| Method | When to use |
|---|---|
| Guided interview | You want the Planner to ask structured questions for the **current file's** missing fields only. |
| Free-text brain dump | You paste goals, constraints, schedule, links, and topics in one message; the Planner applies what belongs to the current gate and asks only for remaining gaps on that file. |
| Existing workspace binding | You already have a Notion page, Todoist project, or Obsidian vaults; used at Gate 3. |
| Manual edit | You copy and edit the current template yourself; the Planner validates and asks for any remaining gaps. |

`personal/goal.md`, `personal/focus-areas/`, `personal/baseline.md`, and `personal/workspaces.md` must be gitignored in the target workspace. Never commit them.

Example onboarding prompt:

```text
Use the polymath-caliper skill in init mode.
Complete personal files one gate at a time (goal.md, then each
focus-areas/<slug>.md, then workspaces.md, then baseline.md).
For the current file only: report what is missing and ask for it.
Do not create Notion or Todoist resources until I approve.
```

## 1. Set up the five components

Do this once during a 60–90 minute setup session after the personal files exist (still under `init`, or immediately after).

### Planner

Create a dedicated chat or workspace named after your system instance (for example the workspace title in `personal/workspaces.md`). Use it only for this system, so its context stays focused.

Start the Planner with this instruction:

```text
Use the polymath-caliper skill. Read its planning-spec.md reference along with
personal/goal.md, personal/focus-areas/, personal/baseline.md, and personal/workspaces.md before acting.
Follow the external-system approval protocol.
```

Use the Planner's supported integrations, APIs, or computer-use capability to connect to the external systems. Review the proposed actions before approving external updates.

### Roadmap & Memory (Notion)

Create a page named with the Notion page title from `personal/workspaces.md`. Use these sections:

1. **Outcome** — from `personal/goal.md`
2. **Baseline Evaluation** — from `personal/baseline.md`
3. **Lanes**
   - Build
   - Reading
   - Open
   - Parking Lot
4. **Weekly plan**
5. **Weekly reviews**
6. **Evidence portfolio**
7. **Decisions**

Create a simple database called **Roadmap Items** with these properties:

| Property | Type | Purpose |
|---|---|---|
| Item | Title | Focus Area, goal, project, skill, or deliverable |
| Lane | Select | Build, Reading, Open, Parking Lot |
| Status | Select | Not started, Active, Blocked, Done |
| Outcome value | Select | High, Medium, Low |
| Evidence | Text / URL | Repository, note, certificate, write-up, or verifiable output |
| Next action | Text | One concrete next step (prefer Baseline starting point) |
| Review date | Date | When the Planner should reconsider it |

Seed items from the Focus Areas in `personal/goal.md` and `personal/focus-areas/`. Leave competing choices in **Parking Lot** until you make an explicit choice.

After the page exists, record its canonical URL in `personal/workspaces.md`. Full rules: [notion-workspace.md](notion-workspace.md).

### Knowledge Base (Obsidian)

You can use one or multiple independent Obsidian vaults on disk, each covering a specific area or domain. Every vault is treated as an equal input to the system (see [obsidian-workspace.md](obsidian-workspace.md)).

Register each vault you want the Planner to read under `## Obsidian` in `personal/workspaces.md`:

| Vault name | Local path | Scoped subfolder (optional) | Areas / topics covered |
|---|---|---|---|
| `<VAULT_NAME_1>` | `<ABSOLUTE_LOCAL_PATH_1>` | `<OPTIONAL_SUBFOLDER>` | `<FOCUS_AREAS_OR_TOPICS>` |
| `<VAULT_NAME_2>` | `<ABSOLUTE_LOCAL_PATH_2>` | `<OPTIONAL_SUBFOLDER>` | `<FOCUS_AREAS_OR_TOPICS>` |

Use this note template inside whichever vault corresponds to the Focus Area you are studying:

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

The Planner must read only the registered vaults (and scoped subfolders) relevant to the current review, treating them as read-only inputs. Keep unmapped vaults and personal notes private unless you explicitly register or share them.

### Task Tracker (Todoist)

Create one project using the project name from `personal/workspaces.md`.

Create these sections:

```text
Inbox
This Week
Today
Waiting / Follow-up
Completed Evidence
```

After creation, record the project ID and each section ID in `personal/workspaces.md`. Full rules: [todoist-workspace.md](todoist-workspace.md).

Use this task format:

```text
[Verb] [specific outcome] — [time estimate]
```

Illustrative examples (adapt to your Goal and Focus Areas):

```text
Implement the next module exercise — 90 min
Write documentation for the milestone project — 45 min
Compare two technical options against target requirements — 60 min
Draft one case study summarizing a completed milestone — 30 min
```

Every task must have:

- One clear outcome
- A time estimate of 25–90 minutes
- A due date only when you genuinely plan to do it
- A link to its source Focus Area, material, or Notion item when useful
- Derivation from the current Baseline starting point when possible

The Planner should propose tasks first. After you approve the week, it can add them to the Task Tracker and update Notion when it has authorized access. Otherwise, it must provide the ready-to-apply changes.

## 2. Define the lanes

Use the lane model customized to your Goal in `personal/goal.md` and each Focus Area file.

| Lane | Purpose | Typical contents |
|---|---|---|
| **Build** | Work that creates verifiable proof now | Priority projects, deliverables, core practice, positioning materials |
| **Reading** | Slow, continuous study | Foundational study, technical reading, synthesis notes |
| **Open** | One limited exploration at a time | A single Focus Area promoted from Parking Lot |
| **Parking Lot** | Valuable ideas without current commitment | Everything not chosen for the present Open lane |

Apply any output or language rules from your Goal across every lane.

Before the first weekly plan, answer the unresolved decisions listed in `personal/goal.md` in your Notion **Decisions** section. Do not work on more than one competing Open-lane choice at the same time.

## 3. Run the first planning session (`roadmap`)

Give the Planner access to the Notion page, relevant Obsidian vaults, and Todoist project if the current tool supports those connections. Configure and authorize only the connections needed for Notion and Todoist. If a connection is unavailable, use the ready-to-apply change set the Planner provides.

Then send this prompt (the skill owns the interview checklist and post-answer deliverables):

```text
Use the polymath-caliper skill in roadmap mode.
```

Review the roadmap before approving it. In particular, check that it includes direct Goal-advancing activity from week one, rather than postponing it until after study goals.

## 4. First-week plan creation

Ask the Planner to propose the first week using this structure:

| Area | Required outcome |
|---|---|
| Goal / positioning | Produce or refine deliverables or proof directly tied to The Goal |
| Primary Build Focus Area | Complete a defined module, exercise, project increment, or write-up |
| Core craft practice | Produce a tangible improvement or artifact tied to the Goal |
| Communication / synthesis | Produce writing, documentation, or presentation practice as required by the Goal |
| Open lane | Explore only the Focus Area you selected |

Approve the plan only if every planned task fits inside the planned execution budget and has a concrete completion condition.

## 5. Daily loop (`daily`)

Every planned workday:

1. Open the Task Tracker (Todoist) and choose only the tasks scheduled for today.
2. Work through the planned daily hours from your Goal.
3. Use remaining daily capacity only for unplanned work, external follow-ups, urgent tasks, or recovery.
4. Tick completed Task Tracker tasks immediately.
5. Add a short Obsidian note in the corresponding vault after meaningful study or project work.
6. If you cannot complete a task, mark it incomplete and write the reason. Do not silently move it forward.

At the end of the day, send the Planner:

```text
Use the polymath-caliper skill in daily mode.
Daily check-in:
Completed:
Not completed:
Blockers:
New evidence or notes:
Available time tomorrow:
```

The Planner should respond with a short proposal for tomorrow grounded in `personal/baseline.md`. It should not reshuffle the whole week without your approval.

## 6. Weekly review (`weekly`)

Create a recurring Task Tracker task: **Weekly review with your Planner** (or the title configured in `personal/workspaces.md`). Record its task ID in `personal/workspaces.md`.

During the review:

1. Export or inspect completed and incomplete Task Tracker tasks.
2. Let the Planner read the week's relevant Obsidian notes from the registered vaults (or share them).
3. Ask the Planner to refresh `personal/baseline.md` and update the Notion roadmap after you approve its review.
4. Decide whether the Open-lane Focus Area stays active, becomes Reading, or returns to Parking Lot.
5. Approve next week's plan within your planned execution budget, using Baseline starting points.

Use this prompt:

```text
Use the polymath-caliper skill in weekly mode.
Run my weekly review.

Read:
- personal/goal.md, personal/focus-areas/, personal/baseline.md, and personal/workspaces.md,
- completed and incomplete Task Tracker (Todoist) tasks,
- this week's Obsidian notes from the relevant registered vaults,
- my Notion roadmap.

Return:
1. completed outcomes and evidence,
2. incomplete tasks with the real reason,
3. progress toward my Goal deadline (updated Baseline draft),
4. risks to the deadline,
5. proposed lane changes,
6. a proposed plan for next week within my planned execution budget,
   derived from Baseline starting points,
7. categories of work that may use the unplanned reserve, without scheduling tasks into it.

Do not update Notion or Todoist until I approve.
```

## 7. Decision rule for every new interest (`evaluate`)

When a new course, technology, or idea appears, do not immediately add it to the Task Tracker.

Add it to Notion's Parking Lot (as a Focus Area or candidate) and ask:

```text
Use the polymath-caliper skill in evaluate mode.
Does this directly improve my chances of achieving The Goal by my deadline?
What current Focus Area or roadmap item would it replace?
What evidence would it produce?
```

Only promote it to the Open lane when you explicitly approve that tradeoff. After approval, refresh `personal/baseline.md`.
