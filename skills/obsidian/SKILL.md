---
name: obsidian
description: coordinate work in an obsidian vault with grounded reading, conservative edits, and para-aware organization. use when answering questions from vault notes, creating or editing notes, organizing notes into para, checking or repairing links after changes, or ingesting transcripts into the vault. for transcript ingestion, route to the /obsidian-ingest command. for note writing or editing, use the obsidian-markdown skill if it is available.
---

# Obsidian vault controller

Treat the vault as the source of truth whenever the task depends on vault content.

## Core rules
- Ground answers and edits in the vault.
- Plan before action.
- Minimize diffs.
- Avoid deleting user content.
- Keep repeated operations idempotent.
- Prefer conservative, reversible changes.

## Read discipline
Before reading notes beyond a quick listing or search, consult `references/read-workflow.md`.

## Write discipline
Before creating, editing, moving, or renaming notes, consult `references/write-routing.md`.

## PARA organization
When placement, note type, or routing is relevant, consult `references/para-routing.md`.

## Integrity after change
After any create, edit, move, or rename, consult `references/integrity-sweep.md`.

## Transcript ingestion
When the user asks to ingest transcripts, check for new transcripts, or process transcript files into the vault, use the `/obsidian-ingest` command rather than reproducing the ingestion procedure inline.

If transcript-specific interpretation or post-ingest note updates are needed, consult `references/transcript-ingestion.md`.

## Tasks
When extracting, formatting, deduplicating, or propagating tasks, consult `references/task-conventions.md`.

## People notes
When updating or using person-specific communication traits, consult `references/people-style-profile.md`.

## Link discovery
When creating a new note manually (not via transcript ingestion), consult `references/link-discovery.md` to suggest related notes and handle explicit connections.

## Interaction style
Be concise. Prefer small, explainable changes. Ask a single bundled clarifying question only when ambiguity would cause wrong filing, wrong linking, or mistaken identity.

## Final output
Use the humanizer skill for the final user-facing response if it is available.