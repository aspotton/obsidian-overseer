# obsidian-overseer

A modular OpenCode setup for working with an Obsidian vault using a thin agent, a dedicated ingest command, and a skill-based control plane.

This repository is designed so each top-level folder can be symlinked into the appropriate global OpenCode config location.

## Why this exists

Most of us are drowning in information but starving for wisdom. We capture bookmarks, save snippets, and record meetings, but that data usually just sits in a digital graveyard. Tiago Forte's "Second Brain" methodology offers a way out. This system relies on the CODE framework: Capture what resonates, Organize for actionability, Distill the essence, and Express your ideas. By offloading the burden of remembering everything to a structured system, you free up your biological brain for what it does best: thinking and creating. This is cognitive offloading in practice. Over time, your vault starts to gain cumulative value as old ideas connect with new ones in ways you didn't expect.

In this system, organization isn't about filing things away by topic. Instead, we use the PARA method: Projects, Areas, Resources, and Archives. This shifts the focus from "where does this belong?" to "when will I use this?" Projects are active efforts with deadlines. Areas cover ongoing responsibilities, while Resources house topics of interest for future use. Archives hold everything else. This structure ensures that the most relevant information is always at your fingertips when you're actually working. It turns a static library into a dynamic workspace.

For developers, a Second Brain is a significant advantage. This is where you keep your debugging journals. You don't just record the fix; you document the "why" behind it so you don't have to solve the same problem twice. Your personal code cookbook lives here too, filled with reusable patterns and snippets that you've actually tested. This also serves as a decision log for architectural choices that seemed obvious at the time but need context six months later. Most importantly, it makes context switching between projects less painful. When you can pull up a project dashboard that links to every relevant meeting, decision, and task, you're back in the flow in minutes instead of hours. Your vault becomes the perfect context for AI-augmented development. When your AI assistant can "read" your notes, it understands your specific patterns and constraints.

The specific problem this repository solves is the "transcript trap." We record meetings and generate high-fidelity transcripts using tools like Whisper, but those transcripts are often too noisy to be useful. They're ephemeral. Obsidian Overseer automates the process of turning that raw noise into searchable knowledge. It pulls transcripts into your vault and performs surgical filtering to remove the fluff. The system extracts structured summaries, key decisions, action items, and open questions. By using bidirectional links, it connects these insights to the right projects and people automatically. This turns a one-hour meeting into a five-minute read that actually helps you get things done.

This setup bridges the gap between your Obsidian vault and your AI assistants via OpenCode. The repository provides a modular, skill-based control plane that understands how to navigate and update your Second Brain without making a mess. We use a thin agent approach to keep things fast and predictable. Storing notes is only the beginning. Our goal is to make your knowledge actionable so you can spend less time searching and more time building.

## What this repo contains

`obsidian-overseer` separates Obsidian behavior into a **parallel subagent architecture** that optimizes performance for three distinct usage patterns:

### Agents

- `agents/obsidian.md`
  A thin Obsidian-focused agent that delegates to the skill router.

- `agents/obsidian-read.md`
  Specialized agent for transcript analysis and extraction (speakers, topics, decisions, action items).

- `agents/obsidian-write.md`
  Specialized agent for note creation and updates with PARA-aware routing.

- `agents/obsidian-ingest-orchestrator.md`
  Central coordinator for parallel transcript processing with 6-phase workflow.

### Commands

- `commands/obsidian-ingest.md`
  A slash command for parallel transcript ingestion into the current vault.

### Skills

- `skills/obsidian/SKILL.md`
  Main router that dispatches tasks to appropriate agents and subagents.

- `skills/obsidian/references/`
  Reference files for specific operations:
  - `read-workflow.md` - Grounded reading discipline
  - `write-routing.md` - Note creation and editing rules
  - `para-routing.md` - Note placement (Projects, Areas, Resources, Archives)
  - `integrity-sweep.md` - Post-write safety checks
  - `transcript-ingestion.md` - Transcript processing rules
  - `task-conventions.md` - Task extraction and formatting
  - `people-style-profile.md` - Communication style profiling
  - `link-discovery.md` - Related note suggestions
  - `ingest-orchestration.md` - Parallel processing rules

The goal is to avoid one large monolithic prompt and instead load only the guidance needed for the current operation, with **parallel processing** for transcript ingestion.

## Repository structure

```text
.
├── AGENTS.md                 # Root project overview with architecture details
├── README.md                 # This file
├── agents/
│   ├── obsidian.md           # Thin Obsidian agent wrapper
│   ├── obsidian-read.md      # Specialized transcript analysis agent
│   ├── obsidian-write.md     # Specialized note creation/update agent
│   └── obsidian-ingest-orchestrator.md # Parallel processing coordinator
├── commands/
│   └── obsidian-ingest.md    # Transcript ingestion command
└── skills/
    ├── AGENTS.md             # Skills directory coordination
    └── obsidian/
        ├── AGENTS.md         # Core Obsidian skill logic
        ├── SKILL.md          # Main router with subagent dispatch
        └── references/
            ├── AGENTS.md       # Reference-level guidance
            ├── ingest-orchestration.md  # Parallel processing rules
            ├── integrity-sweep.md
            ├── link-discovery.md
            ├── para-routing.md
            ├── people-style-profile.md
            ├── read-workflow.md
            ├── task-conventions.md
            ├── transcript-ingestion.md
            └── write-routing.md
```

The `AGENTS.md` files form a hierarchical knowledge base:
- `./AGENTS.md`: Root project overview
- `skills/AGENTS.md`: Skill-level coordination
- `skills/obsidian/AGENTS.md`: Obsidian-specific skill logic
- `skills/obsidian/references/AGENTS.md`: Reference-level guidance

## Architecture

This repo uses a **parallel subagent architecture** that optimizes performance for three distinct usage patterns:

### Three Usage Patterns

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

### Performance

#### Performance Comparison

| Use Case | Current | New Architecture | Improvement |
|----------|---------|------------------|-------------|
| General queries | Fast | Fast (unchanged) | 0% |
| Simple edits | Moderate | Moderate | 0% |
| Complex edits | Moderate | Faster | +20-30% |
| Ingestion (1 transcript) | Slow | Moderate | +30-40% |
| Ingestion (5+ transcripts) | Very slow | Fast | **+60-70%** |

#### Expected Processing Times

| Batch Size | Expected Time | Notes |
|------------|---------------|-------|
| 1 transcript | 30-40s | Single subagent path |
| 5 transcripts | 45-60s | Full parallelization |
| 10 transcripts | 60-90s | Two batches of 5 |
| 20 transcripts | 2-3 min | Four batches of 5 |

#### 6-Phase Ingestion Workflow

1. **Discovery & Planning** - Scan intake folders, build ingestion plan
2. **Parallel Read Dispatch** - Dispatch Read Subagents (max 5 concurrent)
3. **Parallel Write Dispatch** - Dispatch Write Subagents (max 5 concurrent)
4. **Integrity Sweep** - Run integrity checks on processed notes
5. **Archive Source Transcripts** - Move processed transcripts to archive
6. **Final Reporting** - Generate comprehensive ingestion report

## How it works

### General Queries (Fast Path)

When you ask a question like "What happened in Project Atlas?", the system:
1. Routes directly to `read-workflow.md`
2. Searches the vault for relevant notes
3. Reads incrementally and answers from what was actually reviewed
4. **No subagent overhead** - completes in ~2-3 seconds

### Note Editing (Optional Optimization)

For note updates like "Update the Atlas project note with the rollout timeline":
1. Routes to `write-routing.md`
2. **Simple edits**: Direct processing (no subagent)
3. **Complex edits**: Optional Write Subagent for multi-file operations
4. Runs integrity sweep after changes

### Transcript Ingestion (Parallel Processing)

When you run `/obsidian-ingest`:

1. **Discovery Phase**: Scan `Inbox/Transcripts/` or `Inbox/Transcriptions/` for unprocessed transcripts
2. **Read Phase**: Dispatch parallel Read Subagents (max 5 concurrent) to analyze transcripts
3. **Write Phase**: Dispatch parallel Write Subagents (max 5 concurrent) to create/update notes
4. **Integrity Phase**: Run integrity sweeps on processed groups
5. **Archive Phase**: Move processed transcripts to `Archive/Transcripts/`
6. **Reporting**: Generate summary of successes, failures, and pending items

**Parallel Processing Rules:**
- Safe to parallelize: Reading different transcripts, writing to independent notes, integrity checks on separate groups
- Cannot parallelize: Multiple writes to the same file, writes with dependencies
- Concurrency limit: 5 subagents per operation type
- Timeout: 60s for reads, 90s for writes, 30s for integrity

**Error Handling:**
- Single subagent failures: Log error, continue with remaining operations
- Never rollback successful writes
- Report partial success with details for manual review

## Reference files

### `references/ingest-orchestration.md`

**NEW**: Parallel processing rules for transcript ingestion:
- Orchestrator responsibilities and 6-phase workflow
- Parallel processing rules (when to parallelize, concurrency limits)
- Synchronization points and barriers between phases
- Error handling and rollback procedures (never rollback successful writes)
- File conflict prevention strategies
- Performance expectations and batching strategies

### `references/read-workflow.md`

Rules for grounded reading:
- create a read plan first
- search before loading notes
- read incrementally
- answer from notes actually reviewed

### `references/write-routing.md`

Rules for note creation and editing:
- create a write plan first
- keep changes minimal
- use `obsidian-markdown` for note-writing behavior if available

### `references/para-routing.md`

Rules for routing notes into:
- `Projects/`
- `Areas/`
- `Resources/`
- `Archive/`
- `Inbox/`
- `Dashboards/`

### `references/integrity-sweep.md`

Rules for post-write safety:
- repair links and embeds
- check for stale references
- propagate updates where needed
- avoid duplicate tasks or ambiguous titles

### `references/transcript-ingestion.md`

Transcript-specific interpretation and update rules:
- intake folders
- speaker identity handling
- transcript conversion to Obsidian markdown
- meeting/person/project note updates
- archive behavior after successful ingest

### `references/task-conventions.md`

Task extraction and propagation rules:
- task formatting
- task placement
- deduplication logic
- ownership handling

### `references/people-style-profile.md`

Guidance for maintaining evidence-based communication and cognition notes for people in the vault.

### `references/link-discovery.md`

Rules for discovering and suggesting related notes when manually creating new content:
- search the vault for potentially related notes
- present 3-5 suggestions with context
- auto-link only explicit connections (project, person, direct mentions)
- leave thematic/implicit connections for user to decide

## Symlink strategy

This repository is intended to be used by symlinking each directory into the appropriate OpenCode global config location.

Typical pattern:

```bash
ln -s /path/to/obsidian-overseer/agents /path/to/global/config/agents
ln -s /path/to/obsidian-overseer/commands /path/to/global/config/commands
ln -s /path/to/obsidian-overseer/skills /path/to/global/config/skills
```

If your OpenCode setup expects the contents rather than the folders themselves, symlink the individual files or directories instead.

Example:

```bash
ln -s /path/to/obsidian-overseer/agents/obsidian.md /path/to/global/config/agents/obsidian.md
ln -s /path/to/obsidian-overseer/commands/obsidian-ingest.md /path/to/global/config/commands/obsidian-ingest.md
ln -s /path/to/obsidian-overseer/skills /path/to/global/config/skills/obsidian
```

Adjust the destination paths to match your environment.

## Expected usage

### General vault questions

Ask normally through the Obsidian agent or any agent that can access the Obsidian skill.

Examples:
- "Find the latest notes about Project Atlas and summarize open questions."
- "What recent meetings mention the migration plan?"

### Note editing

Ask for the change normally.

Examples:
- "Update the Atlas project note with the rollout timeline."
- "Add these action items to the meeting note and related project page."

### Transcript ingestion

Run:

```text
/obsidian-ingest
```

This is the explicit entrypoint for processing transcripts from the vault intake area.

## Recommended companion skills

This repo works best with these optional companion skills installed:

### 1. `obsidian-markdown` skill
**Purpose**: Specialized markdown formatting for Obsidian notes (wikilinks, embeds, callouts, properties).

**Installation**: https://github.com/kepano/obsidian-skills/tree/main/skills/obsidian-markdown

**Benefit**: When available, `write-routing.md` directs note-writing and note-editing tasks to this skill so markdown formatting rules are not duplicated here. This keeps `obsidian-overseer` focused on vault behavior, routing, note placement, integrity, and transcript handling, while leaving markdown authoring to the specialist skill.

### 2. `humanizer` skill
**Purpose**: Remove AI-writing patterns from text to make it sound more natural and human-written.

**Installation**: https://github.com/blader/humanizer

**Benefit**: Used for final user-facing responses to ensure natural-sounding communication that doesn't reveal AI-generated writing patterns.

**Note**: Both skills are optional. The core functionality works fine without them, but installing these skills significantly improves the quality of output.

## Philosophy

This repo favors:

- grounded answers over memory
- planning before reading or writing
- small, reversible changes
- minimal duplication
- idempotent repeated runs
- conservative handling of ambiguous identities, links, and routing
- automatic link discovery for manual note creation

## Notes

- Transcript intake may use either `Inbox/Transcripts/` or `Inbox/Transcriptions/`, depending on vault convention.
- PARA routing defaults are conservative and try to avoid creating new top-level structures unnecessarily.
- If speaker identity or routing is ambiguous, the system should prefer conservative handling rather than confident guessing.
