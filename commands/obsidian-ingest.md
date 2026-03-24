---
description: Read transcripts and ingest them into the current Obsidian vault via the ingest orchestrator
agent: obsidian
subtask: true
orchestrator: ingest-orchestration
---

There are new transcripts available for ingesting in `Inbox/Transcripts` or `Inbox/Transcriptions`.

This command triggers the **ingest orchestrator** which manages parallel transcript processing through the `obsidian` skill.

## Orchestrator Workflow

The ingest orchestrator coordinates the following phases:

1. **Discovery Phase**: Scan intake folders for unprocessed transcripts
2. **Read Phase**: Dispatch parallel Read Subagents (max 5 concurrent) to analyze transcripts
3. **Write Phase**: Dispatch parallel Write Subagents (max 5 concurrent) to create/update notes
4. **Integrity Phase**: Run integrity sweeps on processed groups
5. **Archive Phase**: Move processed transcripts to `Archive/Transcripts/`
6. **Reporting**: Generate summary of successes, failures, and pending items

## Processing Rules

Transcripts are processed in time order from oldest to most recent, determined by:
- File name timestamp (if present)
- File modification timestamp
- Content date metadata (if available)

### Per-Transcript Processing

For each transcript, the orchestrator ensures:
- Analyze and understand the content to be ingested
- Incorporate the knowledge into the vault
- Preserve URLs and any audio or video recording links
- Ensure the resulting notes are searchable and properly linked
- Update outstanding TODO items where appropriate
- Correct obvious spelling mistakes in names, businesses, places, or projects only when confidence is high
- After successful ingestion, move the source transcript to `Archive/Transcripts` and ensure links remain correct and relative

## Reference Files

The orchestrator uses the following `obsidian` reference files:
- `ingest-orchestration` - Orchestration logic and parallel processing rules
- `transcript-ingestion` - Transcript-specific interpretation and update rules
- `task-conventions` - Task extraction and formatting
- `para-routing` - Note placement rules
- `integrity-sweep` - Post-write safety checks
- `read-workflow` - Grounded reading discipline
