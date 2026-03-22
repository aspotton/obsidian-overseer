# AGENTS.md (Obsidian Skill)

## OVERVIEW
Core control plane for Obsidian vault operations. Manages routing to specialized reference files for reading, writing, organization, and ingestion.

## CORE RULES
- **Grounded Answers**: Answer only from notes actually read.
- **Plan Before Action**: Create read/write plans before execution.
- **Minimal Changes**: Patch smallest relevant sections.
- **Conservative Routing**: Ask if PARA placement or identity is ambiguous.
- **Idempotency**: Ensure repeated runs produce the same result.

## READ DISCIPLINE
- **Search First**: Use search tools before loading specific notes.
- **Incremental Loading**: Read notes in stages; don't ingest the whole vault.
- **Verification**: Confirm note content matches expectations before summarizing.
- **Reference**: [[references/read-workflow.md]]

## WRITE DISCIPLINE
- **Write Plan**: Define exactly what will be changed and where.
- **Small Patches**: Use `Edit` tool for surgical updates; avoid full-file rewrites.
- **Markdown Specialist**: Delegate formatting to `obsidian-markdown` skill.
- **Reference**: [[references/write-routing.md]]

## PARA ROUTING
- **Projects**: Active tasks with a deadline.
- **Areas**: Ongoing responsibilities.
- **Resources**: Interests and reference material.
- **Archive**: Completed or inactive items.
- **Inbox**: Default for new, unclassified notes.
- **Dashboards**: High-level overviews and MOCs.
- **Reference**: [[references/para-routing.md]]

## INTEGRITY
- **Link Repair**: Fix broken wikilinks after moves or renames.
- **Alias-First**: Keep old titles as aliases when renaming to preserve inbound links.
- **Deduplication**: Check for existing notes before creating new ones.
- **Reference**: [[references/integrity-sweep.md]]

## TRANSCRIPT INGESTION
- **Intake**: Process from `Inbox/Transcripts` or `Inbox/Transcriptions`.
- **Speaker ID**: Map speakers to person notes; ask if ambiguous.
- **Archiving**: Move processed files to archive after successful ingestion.
- **Reference**: [[references/transcript-ingestion.md]]

## REFERENCES
- [[SKILL.md]]: Main skill router.
- [[references/read-workflow.md]]: Grounded reading discipline.
- [[references/write-routing.md]]: Note creation/editing rules.
- [[references/para-routing.md]]: Note placement rules.
- [[references/integrity-sweep.md]]: Post-change safety.
- [[references/transcript-ingestion.md]]: Transcript processing.
- [[references/task-conventions.md]]: Task extraction/formatting.
- [[references/people-style-profile.md]]: Communication profiles.
