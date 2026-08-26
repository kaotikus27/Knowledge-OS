# Skill: New Project

Scaffold a new project under `02 - Projects/` by interviewing the user, duplicating the shared
template, and writing the project's authority files in this vault's actual schema — not a
generic numbered-folder template.

--- 
## How It Works 
1. **Interview:** Ask the user **one question at a time** (6 questions max).
2. **Duplicate Template:** Copy `02 - Projects/(PROJECT TEMPLATE)/` into the designated lifecycle folder (`02 - Projects/Active/` or `02 - Projects/Incubating/`). This preserves the local `.obsidian` config and plugins so every project opens with the same Obsidian workspace setup. 
3. **Generate Authority Files:** Write `PROJECT-CONTEXT.md` and continuity files (`memory.md`, `handoff.md`, `DECISIONS.md`, `backlog.md`) if the project warrants them based on its lifecycle status. 
4. **Sensible Defaults:** If the user doesn't have an answer yet, use a sensible default and leave a `TODO` rather than pushing for detail they don't have.  

---

## Interview Questions (Ask One at a Time)

Ask conversationally using AskUserQuestion where a discrete choice fits, open conversation
otherwise. Each answer can shape the next question. "Not sure yet" is a fine answer — work with
what you have.

### 1. What's the project called?
Used for the folder name.

### 2. Where does it start — Active, or Incubating?
Per `02 - Projects/Projects-MOC.md`'s lifecycle (`Idea → Incubating → Active → On-Hold or
Completed → Archive`): if there's real work starting now, it's Active. If it's still an idea
being weighed, it's Incubating. Default to **Active** if the user is unsure — that's the more
common case when someone is asking to create a project right now.

### 3. What is this project?
One paragraph — what's being built/done, who it's for. Becomes `PROJECT-CONTEXT.md`'s Objective.

### 4. What does "done" or the current phase's exit criteria look like?
Becomes `PROJECT-CONTEXT.md`'s Current Phase.

### 5. Any known constraints?
Delivery, technology, cost, privacy, compatibility — whatever's already fixed. Becomes
`PROJECT-CONTEXT.md`'s Constraints. Skip if genuinely none yet.

### 6. Does this project need its own standing rulebook?
Some projects carry a recurring checklist/standard the team must keep meeting (e.g. the
E-Commerce project's `AGENTS.md` holds a CX quality checklist re-audited each session). Most
projects don't need this on day one. Ask, and only create a project `AGENTS.md` if they describe
something concrete — otherwise skip it and they can add one later.

## After the Interview

### Step 1: Duplicate the template

```bash
cp -R "02 - Projects/(PROJECT TEMPLATE)" "02 - Projects/[Active or Incubating]/[Project Name]"
```

The template currently contains only `.obsidian/` (Obsidian config and installed plugins) and
nothing else — there are no placeholder content files to clean up. Everything in the section
below is written fresh.

### Step 2: Decide whether this needs continuity files

Per `02 - Projects/Projects-MOC.md`'s Project Context Rule: create `PROJECT-CONTEXT.md`,
`DECISIONS.md`, `memory.md`, and `handoff.md` **only** once a project is Active or complex enough
to need persistent continuity. If the project is Incubating, write just a short `README.md`
stating the idea and skip the rest until it's promoted to Active.

For an Active project, write all of the following:

### Step 3: `PROJECT-CONTEXT.md`

```markdown
---
title: Project Context
type: project-context
status: active
owner: "[owner name from me.md]"
created: [today]
updated: [today]
ai_access: internal
ai_generated: true
review_status: draft
---

# Project Context

## Objective

[From question 3]

## Users

[Who this serves — skip or write "N/A" if not user-facing]

## Constraints

[From question 5, or "None confirmed yet."]

## Current Phase

[From question 4 — the active phase and its exit criteria]

## Authorities

- `memory.md` — durable decisions and constraints.
- `handoff.md` — current execution state and next action.
- `DECISIONS.md` — decision log.
- `backlog.md` — open backlog.
[Add `AGENTS.md` to this list only if Step 6 created one.]
```

### Step 4: `memory.md`

```markdown
---
title: "[Project Name] — Project Memory"
type: memory
status: active
owner: "[owner]"
created: [today]
updated: [today]
ai_access: internal
ai_generated: true
review_status: draft
canonical: true
---

# Purpose

Durable, reviewed decisions and constraints for [Project Name]. Not a session transcript.

# Durable Decisions and Constraints

_None yet — populate as real decisions get made._

# Long-Lived Constraints

_None yet._
```

### Step 5: `handoff.md`

```markdown
---
title: "[Project Name] — Handoff"
type: handoff
status: active
owner: "[owner]"
created: [today]
updated: [today]
ai_access: internal
ai_generated: true
review_status: draft
canonical: true
---

# Current Objective

Just created — getting started.

# Current Status

Project scaffolded, no work done yet.

# Decisions Made

None yet.

# Files Changed

None yet.

# Open Issues

None yet.

# Next Action

[Whatever the user says the first concrete step is — ask if not already covered.]

# Risks

None currently.

# Validation Status

Not applicable yet.

Replace and refresh this file each session.
```

### Step 6: `DECISIONS.md`

```markdown
# Decisions

| ID | Date | Decision | Status | Consequence |
| --- | --- | --- | --- | --- |

Do not mark proposed decisions accepted without owner approval.
```

### Step 7: `backlog.md`

```markdown
# Backlog

Open, not-yet-scheduled work. Carried forward across sessions — check off and move to
`handoff.md`/`DECISIONS.md` once actually picked up.

- [ ] Before any real production deployment, review `05 -Skills/production-security-checklist.md`
      against this project's actual codebase (exposed keys, auth bypass, input validation,
      row-level data scoping, outdated dependencies).
- [ ] _(add items as they come up)_
```

### Step 8: `README.md`

Short plain-language readme: what the project is, one line pointing readers at
`PROJECT-CONTEXT.md` and `AGENTS.md` (the router) for the full picture.

### Step 9: `docs/`

Create an empty `docs/` folder. This is where "how the thing actually works" documentation goes
as it gets built — not duplicated into `PROJECT-CONTEXT.md`.

### Step 10: Project `AGENTS.md` (only if question 6 warranted it)

Only if the user described a concrete recurring standard/checklist. Structure it like the
E-Commerce project's: a short Purpose section explaining what it's a rulebook for, the standard
itself, and a dated "Compliance Snapshot" that gets re-audited and updated in the same change
that closes a gap — never let the snapshot go stale relative to the standard.

### Step 11: No root index to update

This vault doesn't maintain a manually-written list of active projects — `Projects-MOC.md`
routes by folder (`Active/`, `Incubating/`, `On-Hold/`, `Completed/`, `Archive/`), and
`AGENTS.md`'s task bundle for "Active project" work already knows to load
`02 - Projects/Active/<Project>/PROJECT-CONTEXT.md` directly. Putting the project in the right
folder *is* the registration — there's nothing else to update.

### Step 12: Confirm to the user

Show them:
- The folder path created and which files were written vs. skipped (and why, if Incubating)
- A one-line summary of the objective and current phase
- If constraints or the standing-rulebook question got skipped, mention they can be added later
