---
title: "Leo's Knowledge OS Architecture"
type: architecture
status: active
version: 1.0.0
owner: "Leo"
created: 2026-08-11
updated: 2026-08-11
ai_access: internal
ai_generated: true
review_status: draft
canonical: true
---

# Leo's Knowledge OS Architecture

## Release

Version `1.0.0`.

## Purpose

A portable operating system for durable knowledge, projects, and active work.

## Principles

1. Markdown files are canonical and portable.
2. Numbered domains provide stable navigation.
3. Durable context, active execution state, generated state, and history remain separate.
4. Active-project context exists only when justified.
5. Templates are tool-independent.
6. Existing information is preserved; archives replace destructive deletion.
7. Secrets and credentials are never committed.
8. External sources default to read-only and unconfigured.

## Domain Contract

| Domain | Responsibility |
| --- | --- |
| `00 - System` | Policies, configuration, automation definitions, state, reports |
| `01 - Business` | Organizations, clients, operations, products, strategy |
| `02 - Projects` | Initiative lifecycle and active execution |
| `03 - Personal` | Private planning and records |
| `04 - Hobbies` | Interests and creative pursuits |
| `05 - Knowledge` | Curated durable knowledge and research |
| `06 - Inbox` | Unprocessed capture |
| `07 - Daily` | Daily and periodic review |
| `08 - Templates` | Reusable structures |
| `09 - Attachments` | Non-Markdown assets |
| `99 - Archive` | Inactive historical material |

## Information Lifecycle

`Capture -> Process -> Connect -> Apply -> Review -> Archive`

## Context Model

Use progressive task-based loading. Reports, state, history, attachments, inventories, and full changelog are exceptional context. Running work is represented by compact handoff state; approved durable intent belongs in memory.

## Versioning

Use Semantic Versioning. Major changes break the folder/metadata/workflow contract; minor changes add compatible capability; patches correct defects.

