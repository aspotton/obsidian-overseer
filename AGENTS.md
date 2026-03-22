# AGENTS.md

## OVERVIEW
Modular OpenCode setup for Obsidian vault integration. Uses thin agent, skill-based routing, and operation-specific references.

## STRUCTURE
```text
.
├── AGENTS.md (this file)
├── README.md
├── agents/
│   └── obsidian.md
├── commands/
│   └── obsidian-ingest.md
└── skills/
    ├── AGENTS.md (target)
    └── obsidian/
        ├── AGENTS.md (target)
        ├── SKILL.md
        └── references/
            ├── AGENTS.md (target)
            ├── integrity-sweep.md
            ├── link-discovery.md
            ├── para-routing.md
            ├── people-style-profile.md
            ├── read-workflow.md
            ├── task-conventions.md
            ├── transcript-ingestion.md
            └── write-routing.md
```

## WHERE TO LOOK
| File | Purpose |
|------|---------|
| `agents/obsidian.md` | Thin agent wrapper; delegates to skill. |
| `commands/obsidian-ingest.md` | Entrypoint for transcript ingestion. |
| `skills/obsidian/SKILL.md` | Main router for vault operations. |
| `references/read-workflow.md` | Grounded reading discipline. |
| `references/write-routing.md` | Note creation/editing rules. |
| `references/link-discovery.md` | Link discovery for manual note creation. |
| `references/para-routing.md` | PARA placement rules. |
| `references/integrity-sweep.md` | Post-change safety checks. |
| `references/transcript-ingestion.md` | Transcript processing logic. |
| `references/task-conventions.md` | Task extraction and formatting. |
| `references/people-style-profile.md` | Communication style profiling. |

## CONVENTIONS
- **Grounded Answers**: Answer only from notes actually read.
- **Plan Before Action**: Create read/write plans before execution.
- **Minimal Changes**: Patch smallest relevant sections.
- **PARA Routing**: Use Projects, Areas, Resources, Archive, Inbox, Dashboards.
- **Integrity Sweep**: Repair links and check for duplicates after edits.
- **Link Discovery**: Suggest related notes when creating new content manually.
- **Idempotency**: Ensure repeated runs produce same result.
- **Link Safety**: Prefer alias-first renaming to preserve inbound links.

## ANTI-PATTERNS
- **Logic Duplication**: Don't repeat rules across files.
- **Confident Guessing**: Ask if identity or routing is ambiguous.
- **Silent Deletion**: Never delete user content without request.
- **Unplanned Reading**: Don't read notes without a plan.
- **Monolithic Prompts**: Avoid putting all logic in the agent file.

## UNIQUE STYLES
- **Skill-Based Routing**: Agent delegates to skill; skill delegates to references.
- **Bundled Clarification**: Ask one multi-part question for all ambiguities.
- **Alias-First Renaming**: Keep old title as alias when moving/renaming.
- **Telegraphic Prose**: Concise fragments, no filler.

## COMMANDS
- `/obsidian-ingest`: Processes transcripts from `Inbox/Transcripts` or `Inbox/Transcriptions`.

## DEPLOYMENT STRATEGY
- **Symlink Folders**: Link `agents/`, `commands/`, and `skills/` to global config.
- **Symlink Files**: Link individual `.md` files if global config expects flat structure.

## NOTES
- Hierarchy of AGENTS.md files:
  - `./AGENTS.md`: Root project overview.
  - `skills/AGENTS.md`: Skill-level coordination.
  - `skills/obsidian/AGENTS.md`: Obsidian-specific skill logic.
  - `skills/obsidian/references/AGENTS.md`: Reference-level guidance.
