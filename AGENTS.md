# AGENTS.md

## OVERVIEW
Modular OpenCode setup for Obsidian vault integration. Uses thin agent, skill-based routing, and operation-specific references with parallel subagent processing for transcript ingestion.

## ARCHITECTURE OVERVIEW

### Three Usage Patterns

The system supports three distinct usage patterns with optimized performance for each:

| Pattern | Use Case | Subagent Overhead | Performance |
|---------|----------|-------------------|-------------|
| **General Queries** | "What happened in Project Atlas?" | None (fast path) | ~2-3 seconds |
| **Note Editing** | "Update the Atlas project note" | Optional (complex edits only) | ~3-10 seconds |
| **Transcript Ingestion** | `/obsidian-ingest` | Full orchestration (parallel) | 60-70% faster for 5+ transcripts |

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         USER INTERACTION LAYER                          │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      THIN AGENT (agents/obsidian.md)                    │
│                    - Minimal wrapper (23 lines)                         │
│                    - Delegates to skill                                 │
│                    - No logic duplication                               │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                    SKILL ROUTER (skills/obsidian/SKILL.md)             │
│                    ┌────────────────────────────────────┐               │
│                    │  Task Type Analysis                │               │
│                    └────────────────────────────────────┘               │
│                            │         │         │                       │
│            ┌───────────────┘         │         └───────────────┐       │
│            ▼                         ▼                         ▼       │
│    ┌───────────────┐         ┌───────────────┐         ┌───────────────┐│
│    │  General      │         │  Note         │         │  Transcript   ││
│    │  Query        │         │  Editing      │         │  Ingestion    ││
│    │  (Fast Path)  │         │  (Optional)   │         │  (Orchestrated)││
│    └───────────────┘         └───────────────┘         └───────────────┘│
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                     ┌───────────────┼───────────────┐
                     ▼               ▼               ▼
         ┌─────────────────┐ ┌─────────────┐ ┌───────────────────────┐
         │  Read Workflow  │ │ Write Route │ │ Ingest Orchestrator   │
         │  (Direct)       │ │ (Direct or  │ │ (Subagent Parallel)   │
         │                 │ │  Subagent)  │ │                       │
         └─────────────────┘ └─────────────┘ └───────────────────────┘
```

### Subagent Roles

| Subagent | File | Role | Responsibilities |
|----------|------|------|------------------|
| **Read Subagent** | `agents/obsidian-read.md` | Transcript analysis specialist | Extract speakers, topics, decisions, action items, open questions from transcripts |
| **Write Subagent** | `agents/obsidian-write.md` | Note creation/update specialist | Create/update meeting notes, person notes, project notes, area notes with proper PARA routing |
| **Orchestrator** | `agents/obsidian-ingest-orchestrator.md` | Central coordinator | Manage 6-phase ingestion workflow, dispatch parallel subagents, run integrity sweeps |

## STRUCTURE
```text
.
├── AGENTS.md (this file)
├── README.md
├── agents/
│   ├── obsidian.md                    # Thin agent wrapper
│   ├── obsidian-read.md               # Read subagent (transcript analysis)
│   ├── obsidian-write.md              # Write subagent (note creation)
│   └── obsidian-ingest-orchestrator.md # Orchestrator (parallel processing)
├── commands/
│   └── obsidian-ingest.md             # Ingestion command entrypoint
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
| `agents/obsidian-read.md` | Read subagent for transcript analysis. |
| `agents/obsidian-write.md` | Write subagent for note creation/updates. |
| `agents/obsidian-ingest-orchestrator.md` | Orchestrator for parallel transcript processing. |
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

## PERFORMANCE

### Performance Comparison

| Use Case | Current | New Architecture | Improvement |
|----------|---------|------------------|-------------|
| General queries | Fast | Fast (unchanged) | 0% |
| Simple edits | Moderate | Moderate | 0% |
| Complex edits | Moderate | Faster | +20-30% |
| Ingestion (1 transcript) | Slow | Moderate | +30-40% |
| Ingestion (5+ transcripts) | Very slow | Fast | **+60-70%** |

### Expected Processing Times

| Batch Size | Expected Time | Notes |
|------------|---------------|-------|
| 1 transcript | 30-40s | Single subagent path |
| 5 transcripts | 45-60s | Full parallelization |
| 10 transcripts | 60-90s | Two batches of 5 |
| 20 transcripts | 2-3 min | Four batches of 5 |

### 6-Phase Ingestion Workflow

1. **Discovery & Planning** - Scan intake folders, build ingestion plan
2. **Parallel Read Dispatch** - Dispatch Read Subagents (max 5 concurrent)
3. **Parallel Write Dispatch** - Dispatch Write Subagents (max 5 concurrent)
4. **Integrity Sweep** - Run integrity checks on processed notes
5. **Archive Source Transcripts** - Move processed transcripts to archive
6. **Final Reporting** - Generate comprehensive ingestion report

## COMMANDS
- `/obsidian-ingest`: Processes transcripts from `Inbox/Transcripts` or `Inbox/Transcriptions`.

## BACKWARD COMPATIBILITY

- General queries bypass subagent overhead entirely (unchanged performance)
- Simple note edits use direct path (no subagent overhead)
- Existing `/obsidian-ingest` command continues to work identically
- All reference files remain unchanged
- Skill routing behavior preserved

## DEPLOYMENT STRATEGY
- **Symlink Folders**: Link `agents/`, `commands/`, and `skills/` to global config.
- **Symlink Files**: Link individual `.md` files if global config expects flat structure.

## NOTES
- Hierarchy of AGENTS.md files:
  - `./AGENTS.md`: Root project overview.
  - `skills/AGENTS.md`: Skill-level coordination.
  - `skills/obsidian/AGENTS.md`: Obsidian-specific skill logic.
  - `skills/obsidian/references/AGENTS.md`: Reference-level guidance.
- Transcript intake may use either `Inbox/Transcripts/` or `Inbox/Transcriptions/`, depending on vault convention.
- PARA routing defaults are conservative and try to avoid creating new top-level structures unnecessarily.
- If speaker identity or routing is ambiguous, the system should prefer conservative handling rather than confident guessing.
