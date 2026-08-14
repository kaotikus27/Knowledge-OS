# Skill: Brain Setup

Interview the user and populate the two files that hold their personal context: `me.md`
(identity and working style) and `GOALS.md` (goals and current progress). This is **not** a
CLAUDE.md generator — this vault's routing lives in `AGENTS.md` and `CONTEXT-POLICY.md`, which
are system infrastructure and must never be touched by this interview.

## When to Use

- `me.md` still has placeholder/thin content, or fields the user hasn't reviewed yet
- `GOALS.md` is empty
- User explicitly asks to set up their profile or goals

If `me.md` already has real content, ask whether they want to redo it from scratch or extend
what's there. Default to extending — this interview should never blow away answered fields.

## What This Skill Owns vs. Doesn't

**Owns (may create/edit):**
- `me.md` — identity, role, working style, rules, strengths/weaknesses
- `GOALS.md` — goals, current state, plan, risks

**Never touches:**
- `AGENTS.md`, `CONTEXT-POLICY.md` — canonical system routing/policy files
- `memory.md`, `handoff.md` at root — those are session-continuity files owned by the
  `weekly-update` skill and ongoing work, not a one-time interview
- Anything under `02 - Projects/` — that's the `new-project` skill's job

## Phase 1: Read Current State

Before asking anything:

```bash
cat "me.md" 2>&1
cat "GOALS.md" 2>&1
```

Note which fields in `me.md` are already filled vs. placeholder, and whether `GOALS.md` has
content. Use this to skip questions that are already answered — confirm briefly instead of
re-asking.

## Phase 2: Interview (4 Rounds)

Conduct conversationally. Summarize what you captured after each round so the user can correct
anything before you move on. Group related sub-questions naturally (3-5 at once is fine if they
flow together). If an answer is thin, probe once, then move on — don't interrogate.

Use AskUserQuestion for the communication-style choice in Round 2. Everything else is open
conversation.

---

### Round 1: Identity

Ask:
- **What should I call you?** (Default: whatever's already in `me.md`'s Preferred name field)
- **What's your role?** One-liner — e.g. "Software Engineer (Angular / Spring Boot)"
- **Timezone and locale?** (Default: keep existing values if already set)
- **What's your purpose or mission** — the thing that drives the work?
- **What do you refuse to do?** Values, hard lines, non-negotiables.

**Goal:** Fill `me.md`'s existing fields (Preferred name, Role, Timezone, Locale, Preferred
language) plus a new freeform note capturing purpose/values in their own words.

---

### Round 2: Working Style & Rules

Start with AskUserQuestion for communication style:

**Question:** "How should Claude communicate with you?"
- **Direct, concise, practical** — no padding, get to the point
- **Supportive but honest** — encourage, but flag real issues when they matter
- **Balanced** — match energy, direct when it counts
- (user can pick Other for custom)

Then ask open-ended:
- **Any specific rules or pet peeves?** (e.g. "don't give me 10 options when one will do")
- **Anything Claude should never do** in this vault?

**Goal:** Fill/update `me.md`'s Communication style field, plus a short "Rules & Boundaries"
list if the user has concrete ones. Concrete beats vague — "stop summarizing at the end of every
response" beats "be concise."

---

### Round 3: Strengths & Weaknesses

Ask:
- **What are you genuinely great at?**
- **What are your blind spots or recurring failure modes?**
- **What do you default to when stressed or overwhelmed?**

**Goal:** A short, honest self-assessment Claude can reference. Specific beats generic —
"I spread too thin across too many projects" beats "bad at time management." This becomes a
new section in `me.md`, not a separate file.

---

### Round 4: Goals & Current Progress

Ask:
- **What's your main goal right now?** Concrete target, milestone, or number.
- **Where are you today?** Current state, progress, resources.
- **What's the plan to get there?** Even rough steps count.
- **Any risks or time-sensitive factors?**

**Goal:** Real numbers and dates wherever possible. This becomes `GOALS.md`. If the plan is
vague, write what they gave you and suggest a Chess Moves session (`02 -Chess Moves (Long-Term
Planning)/`) to flesh it out later — don't fabricate detail they didn't give you.

---

## Phase 3: Write the Files

### `me.md` — extend the existing frontmatter/fields, don't replace the schema

```markdown
---
title: Working Profile
type: ai-instruction
status: active
owner: "[Name]"
created: [keep original date if updating]
updated: [today]
ai_access: internal
ai_generated: true
review_status: draft
canonical: true
---

# Working Profile

- Preferred name: [Round 1]
- Optional full name:
- Public author name:
- Technical username:
- Role: [Round 1]
- Timezone: [Round 1]
- Locale: [Round 1]
- Preferred language: [Round 1]
- Communication style: [Round 2]

## Purpose & Values

[Round 1 — purpose/mission and hard lines, in their own words. 2-3 sentences, not a bio.]

## Rules & Boundaries

[Round 2 — bulleted list of concrete rules, only if they gave any. Omit section if none.]

## Strengths & Weaknesses

**Strengths:** [Round 3]
**Blind spots:** [Round 3, including their stress-default pattern]

Review this generated profile before operational use. Remove any field you do not want
available to AI tools.
```

### `GOALS.md`

```markdown
---
title: Goals
type: goals
status: active
owner: "[Name]"
created: [today]
updated: [today]
ai_access: internal
ai_generated: true
review_status: draft
---

# Goals

## Current Objective

[Round 4 — the concrete target]

## Current State

[Round 4 — where they are today, real numbers]

## Plan

[Round 4 — steps, even rough ones]

## Risks & Time-Sensitive Factors

[Round 4, or "None noted."]
```

**Critical rules for writing:**
- **Don't fabricate.** Thin answer → thin section. Never pad with assumptions.
- **Use their words.** Don't polish "I build cool shit with AI" into "innovative AI solutions."
- **Preserve existing content.** If a field already had a real (non-placeholder) value and the
  user didn't change it this round, keep it as-is — don't overwrite with a reworded version.
- **Follow `00 - System/Metadata-Standards.md`** for frontmatter — `ai_access`, `review_status`,
  `owner`, `created`/`updated` are not optional decoration, they're how this vault's routing and
  privacy rules work.

## Phase 4: Review & Confirm

Show the full diff of what's changing in `me.md` and `GOALS.md`. Ask:

> "Here's what I've got — anything to change before I save it?"

Make targeted edits to the specific parts flagged. Only write after explicit confirmation.

## Phase 5: Next Steps

After writing, tell the user:

1. **`me.md` and `GOALS.md` are live** — `AGENTS.md`'s bootstrap reads `me.md` on every task
2. **Create your first project** — use the `new-project` skill to scaffold something under
   `02 - Projects/Active/`
3. **If goals feel vague** — a Chess Moves session can help flesh out the plan
4. **Keep `memory.md`/`handoff.md` current** — that's the `weekly-update` skill's job, not this one
