# Knowledge OS — Claude Code System Rules & Guardrails

## 1. Real-Time Context & Usage Monitoring Protocol
- **Proactive Context Warning (80%–90% Threshold):**
  - Monitor conversation length and tool output density across session turns.
  - If conversation history contains heavy file dumps, multiple tool runs, or long execution logs approaching high token limits:
    - **WARN THE USER:** Alert that context depth is nearing ~80% capacity (>120k tokens).
    - **RECOMMEND ACTION:** Advise running `/compact` (to compress history while keeping context) or `/clear` (if starting a new task).
- **Pre-Flight High-Token Task Checkpoint:**
  - Before executing multi-file reads, directory listings, or script executions touching >5 files or >20,000 words:
    - Pause and notify the user: *"This task is token-heavy. Consider checking live session quota with `/usage` before proceeding."*
    - Summarize the execution plan in 2–3 lines before invoking tools.
- **High-Efficiency Execution:**
  - Never output raw code files or logs over 100 lines into chat. Write output directly to target files.
  - Read files in targeted chunks rather than loading entire directory trees.

## 2. Token Economy & Subagent Policy (STRICT)
- **DO NOT SPAWN SUBAGENTS:** Never launch parallel background "reader", "researcher", or "auditor" subagents under any circumstances.
- **Sequential Tool Execution:** Perform all file reads, directory listings, and edits sequentially within the main thread.
- **Targeted Scope Only:** Inspect only the specific folders or files requested by the user. Never perform broad root-level folder scans or global `grep`/`find` searches unless explicitly instructed.
- **Compact Output:** Do NOT dump raw inventories, long file contents, or dozens of individual defects directly into the chat response. Write detailed analysis to a file (e.g., `_archive/audit-log.md`) and provide a brief high-level summary in chat.

## 3. Vault Architecture & Folder Structure
This vault operates on a structured schema. Respect the following directory hierarchy:
- `02 - Projects/`: Contains all active, incubating, and archived projects.
  - `02 - Projects/Active/`: Projects with active execution.
  - `02 - Projects/Incubating/`: Concept-stage projects.
  - `02 - Projects/(PROJECT TEMPLATE)/`: The master template for duplicating new projects.
- `05 - Skills/`: Holds skill/workflow definitions and procedural templates (e.g., `05 - Skills/new-project/`).

## 4. Project Creation & Reorganization Schema
When scaffolding, migrating, or reorganizing an Active project:
- **Preserve `.obsidian`:** Always keep or duplicate the local `.obsidian/` directory (config and workspace plugins).
- **Core Authority Files:** Every active project MUST maintain the following root files in its directory:
  1. `PROJECT-CONTEXT.md` (Objective, Users, Constraints, Current Phase, Authorities)
  2. `memory.md` (Durable decisions and constraints)
  3. `handoff.md` (Current execution state, open issues, and immediate next action)
  4. `DECISIONS.md` (Tabular decision log)
  5. `backlog.md` (Unscheduled tasks)
  6. `README.md` (Short plain-language overview pointing to authority files)
- **Documentation:** All operational "how-to" documentation and reference materials go inside a `docs/` subfolder, not scattered in the project root.

## 5. Markdown Formatting & Metadata Rules
- **Frontmatter:** Always format frontmatter using valid YAML enclosed in `---` blocks. Include essential metadata (`title`, `type`, `status`, `created`, `updated`, `ai_generated`).
- **File Safety:** Never delete existing project notes or source code during a reorganization. Move unclassified or legacy files into an `_unmapped/` or `_archive/` subfolder for user review.

## 6. Production Deployment Pre-Flight
- Before any project's real production deployment (or a production-deployment planning doc), read `05 -Skills/production-security-checklist.md` and walk through it against the actual codebase — do not just assume its items are handled.
- This applies to any project in this vault heading toward production, not one specific stack.