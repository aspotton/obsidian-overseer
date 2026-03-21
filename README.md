# obsidian-overseer

A modular OpenCode setup for working with an Obsidian vault using a thin agent, a dedicated ingest command, and a skill-based control plane.

This repository is designed so each top-level folder can be symlinked into the appropriate global OpenCode config location.

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
agents/
  obsidian.md

commands/
  obsidian-ingest.md

skills/
  SKILL.md
  references/
    integrity-sweep.md
    para-routing.md
    people-style-profile.md
    read-workflow.md
    task-conventions.md
    transcript-ingestion.md
    write-routing.md
```

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

## Recommended companion skill

This repo is designed to work especially well with an `obsidian-markdown` skill.

When available, `write-routing.md` should direct note-writing and note-editing tasks to that skill so markdown formatting rules are not duplicated here.

This keeps `obsidian-overseer` focused on:
- vault behavior
- routing
- note placement
- integrity
- transcript handling

and leaves markdown authoring behavior to the specialist skill.

## Philosophy

This repo favors:

- grounded answers over memory
- planning before reading or writing
- small, reversible changes
- minimal duplication
- idempotent repeated runs
- conservative handling of ambiguous identities, links, and routing

## Notes

- Transcript intake may use either `Inbox/Transcripts/` or `Inbox/Transcriptions/`, depending on vault convention.
- PARA routing defaults are conservative and try to avoid creating new top-level structures unnecessarily.
- If speaker identity or routing is ambiguous, the system should prefer conservative handling rather than confident guessing.
