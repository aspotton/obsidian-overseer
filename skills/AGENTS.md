# AGENTS.md (Skills)

## OVERVIEW
Control plane for Obsidian vault operations. Manages skill-based routing to specialized reference files. Reduces context overhead by loading only relevant guidance.

## STRUCTURE
```text
skills/
├── AGENTS.md (this file)
└── obsidian/
    ├── AGENTS.md (skill-level logic)
    ├── SKILL.md (main router)
    └── references/
        ├── AGENTS.md (reference-level guidance)
        ├── integrity-sweep.md
        ├── para-routing.md
        ├── people-style-profile.md
        ├── read-workflow.md
        ├── task-conventions.md
        ├── transcript-ingestion.md
        └── write-routing.md
```

## SKILL ROUTING
- **Entrypoint**: `agents/obsidian.md` delegates to `skills/obsidian/SKILL.md`.
- **Router**: `SKILL.md` analyzes the task and selects the appropriate reference.
- **Delegation**:
  - Reading/Querying -> `read-workflow.md`
  - Writing/Editing -> `write-routing.md`
  - Organization -> `para-routing.md`
  - Post-Edit Checks -> `integrity-sweep.md`
  - Transcripts -> `transcript-ingestion.md`
  - Tasks -> `task-conventions.md`
  - People Notes -> `people-style-profile.md`

## CONVENTIONS
- **Thin Router**: `SKILL.md` should contain minimal logic; delegate to references.
- **Context Efficiency**: Only load the specific reference file needed for the current step.
- **Separation of Concerns**: Keep markdown formatting in `obsidian-markdown` skill; keep vault logic here.
- **Hierarchical Guidance**:
  - Root `AGENTS.md`: Project-wide overview.
  - `skills/AGENTS.md`: Skill-level coordination (this file).
  - `skills/obsidian/AGENTS.md`: Obsidian-specific skill logic.
  - `skills/obsidian/references/AGENTS.md`: Reference-level guidance.

## ANTI-PATTERNS
- **Logic Duplication**: Don't repeat rules across files.
- **Confident Guessing**: Ask if identity or routing is ambiguous.
- **Silent Deletion**: Never delete user content without request.
- **Unplanned Reading**: Don't read notes without a plan.
- **Monolithic Prompts**: Avoid putting all logic in the agent file.

## REFERENCES
- [Root AGENTS.md](../AGENTS.md)
- [Obsidian Skill AGENTS.md](obsidian/AGENTS.md)
