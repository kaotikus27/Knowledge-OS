# Skill: Weekly Update

Interview the user to refresh continuity across the vault — root `handoff.md` and `GOALS.md`,
plus each Active project's `handoff.md` (and `memory.md`/`DECISIONS.md`/`backlog.md` when
something durable actually changed). Keeps everything current without letting these files grow
without bound.

## When to Use

- User says "weekly update", "let's do a weekly review", "update my vault", or similar
- It's been a week or more since `handoff.md`'s `updated` frontmatter date

## How It Works

1. Scan root and per-project continuity files to understand current state
2. Interview at the meta level (root `handoff.md` + `GOALS.md`)
3. Walk each Active project for a status check-in
4. Update all files, respecting each file's token budget
5. Optionally create a dated review note in `04 -Reviews/`

---

## Phase 1: Scan Current State

Read everything before asking a single question:

```bash
cat "handoff.md"
cat "GOALS.md"
ls -d "02 - Projects/Active"/*/ 2>&1 || echo "No active projects"
```

For each active project folder, read its `handoff.md` (Current Objective, Open Issues, Next
Action) and `PROJECT-CONTEXT.md` (Current Phase). Build a mental map of what was true last time
so you can ask targeted questions — not make the user repeat everything from scratch.

---

## Phase 2: Meta-Level Interview

Covers root `handoff.md` and `GOALS.md`. Reference what you read in Phase 1 so the user knows
you're caught up.

### Root Handoff Check-In

Show the user the current `Current Objective` / `Open Issues` / `Next Action` from root
`handoff.md`, then ask:

- **What's the current objective now** — same as last time, or has it moved?
- **Anything resolved, blocked, or newly open** since last time?
- **What's the next concrete action?**

If nothing changed, the user can say "same" and the file is left alone.

### Goals Check-In

Reference `GOALS.md`'s Current Objective and Current State. Ask:

- **Any progress since last time?** New numbers, milestones, setbacks.
- **Has the plan changed?**
- **Anything new on risks or time-sensitive factors?**

**Keep this tight.** If nothing changed, move on — don't make them re-justify existing goals.

---

## Phase 3: Project Check-Ins

Walk each folder under `02 - Projects/Active/`. For each one:

1. Show the current `handoff.md` Current Objective / Open Issues
2. Ask: **"What's the update on [Project Name]?"**
3. If nothing changed, they say "no change" and you move on
4. If a project's state has fundamentally changed (shipped, paused, abandoned), flag that this
   is a **move**, not just an update — see Phase 4's note on lifecycle moves

**Keep this fast.** One question per project, a follow-up only if something significant happened.

---

## Phase 4: Update Files

Show the user a summary of every edit before writing.

### Root `handoff.md`

Replace-and-refresh (this file tracks *current* state, not history) — update `Current
Objective`, `Open Issues`, `Next Action`, and bump the `updated` frontmatter date. Don't let it
exceed roughly 2,000 tokens; if it's grown past that, trim resolved issues rather than deleting
the file's structure.

### `GOALS.md`

Only touch sections the user actually updated. Bump `updated`.

### Each active project's `handoff.md`

Same replace-and-refresh treatment as root. Only durable, still-relevant decisions get promoted
to that project's `memory.md` — everything else stays in `handoff.md` and gets replaced next time.

### Project `memory.md` — only if something durable changed

`memory.md` is durable and reviewed, not a running log. Only append here if the user described
an actual standing decision or constraint (something that should survive past this session) —
not routine progress. If nothing durable happened, leave it untouched. Keep it under roughly
3,000 tokens; review the file if it's crept past 4,000.

### Project `DECISIONS.md` — only if a real decision was made

Add a row (`ID | Date | Decision | Status | Consequence`) only for decisions actually accepted
by the owner this session — never mark a proposed decision as accepted without the owner saying so.

### Project `backlog.md`

Add newly-mentioned open items, check off anything the user says is done or already moved into
`handoff.md`/`DECISIONS.md`.

### Lifecycle moves (project state actually changed)

If Phase 3 surfaced a project that's genuinely done, paused, or dead, don't just edit its
`handoff.md` in place — move the whole folder per `Projects-MOC.md`'s lifecycle
(`Active → On-Hold or Completed → Archive`):

```bash
mv "02 - Projects/Active/[Project Name]" "02 - Projects/[Completed or On-Hold or Archive]/[Project Name]"
```

Per `Projects-MOC.md`'s Archive Guidance: before moving to Archive, make sure `DECISIONS.md`
captures the decisions worth preserving and `handoff.md` has one final, clean status — don't let
completed execution history pile up in an active-style handoff file.

---

## Phase 5: Weekly Review Note (Optional)

After all files are updated, ask:

> "Want me to write a review note for this week?"

**If yes:** Create `04 -Reviews/YYYY-MM-DD - Weekly Review.md` (per
`00 - System/Naming-Conventions.md`'s time-based record format), using the interview answers as
content. Check `04 -Reviews/` for a prior note first and match its format if one exists.

**If no:** Skip it, don't push.

---

## Summary of What Gets Touched

| File | What changes |
|------|-------------|
| Root `handoff.md` | Current Objective, Open Issues, Next Action, `updated` date |
| `GOALS.md` | Only sections the user says changed |
| Each active project's `handoff.md` | Full replace-and-refresh |
| Project `memory.md` | Only if a durable decision/constraint was made |
| Project `DECISIONS.md` | Only owner-accepted decisions |
| Project `backlog.md` | New items added, done items checked off |
| Project folder location | Only on an actual lifecycle move (Active → On-Hold/Completed/Archive) |
| `04 -Reviews/YYYY-MM-DD - Weekly Review.md` | Optional, user's choice |

**Never touched by this skill:** `AGENTS.md`, `CONTEXT-POLICY.md`, `me.md` — those are router,
policy, and identity files, not weekly-cadence content.
