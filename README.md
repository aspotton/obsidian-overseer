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

`obsidian-overseer` separates Obsidian behavior into three parts:

- `agents/obsidian.md`
  A thin Obsidian-focused agent that delegates most behavior to the skill.

- `commands/obsidian-ingest.md`
  A slash command for transcript ingestion into the current vault.

- `skills/`
  A compact Obsidian skill with reference files for read workflow, write routing, PARA routing, transcript ingestion, integrity checks, task handling, and people-note style profiling.

The goal is to avoid one large monolithic prompt and instead load only the guidance needed for the current operation.

## Repository structure

```text
.
├── AGENTS.md                 # Root project overview (hierarchical knowledge base)
├── README.md                 # This file
├── agents/
│   └── obsidian.md           # Thin Obsidian agent wrapper
├── commands/
│   └── obsidian-ingest.md    # Transcript ingestion command
└── skills/
    ├── AGENTS.md             # Skills directory coordination
    └── obsidian/
        ├── AGENTS.md         # Core Obsidian skill logic
        ├── SKILL.md          # Main router for vault operations
        └── references/
            ├── AGENTS.md       # Reference-level guidance
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

## Design

This repo uses a controller-style approach:

- The agent is intentionally minimal.
- The skill acts as the main Obsidian control plane.
- The reference files hold operation-specific guidance.
- The command provides an explicit entrypoint for transcript ingestion.

### Why this layout exists

This structure is meant to reduce context usage and improve maintainability.

Instead of keeping all Obsidian behavior in a single agent file, responsibilities are split by operation:

- read/query behavior
- write/edit behavior
- PARA routing
- transcript ingestion guidance
- link and reference integrity checks
- task conventions
- people note style profiling

This lets OpenCode load only the relevant guidance for a given task.

## How it works

### Agent

`agents/obsidian.md` is a thin vault specialist.

It should:
- treat the vault as the source of truth
- rely on the Obsidian skill for detailed operating rules
- stay conservative about edits
- avoid duplicating workflow details inline

### Command

`commands/obsidian-ingest.md` defines `/obsidian-ingest`.

Use it when you want to:
- check for new transcripts
- process transcript files into the vault
- archive processed transcripts after ingestion

### Skill

`skills/SKILL.md` is the main router.

It decides whether the current task is primarily:
- reading/querying the vault
- writing or editing notes
- organizing notes with PARA rules
- running transcript-ingestion-related logic
- performing post-edit integrity work

It then points to the relevant reference file.

## Reference files

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
