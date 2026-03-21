# Transcript ingestion guidance

Use this reference when processing transcript content into the vault after the `/obsidian-ingest` command is invoked, or when transcript-specific interpretation is needed.

## Intake folders
Treat these as possible intake folders:
- `Inbox/Transcripts/`
- `Inbox/Transcriptions/`

Prefer the vault's existing naming if only one is used.

## Ingest goals
For each transcript:
- analyze and understand the content
- preserve useful URLs and recording links
- convert plain text transcripts into Obsidian markdown when saving into the vault
- make the resulting knowledge searchable
- link it to people, projects, areas, and related resources
- update outstanding TODO items when appropriate
- archive the source transcript after successful ingestion if that is part of the requested workflow

## Read planning for transcript ingestion
For each candidate transcript, include in the read plan:
- the transcript file itself
- likely related people notes
- likely related project or area notes
- 1 to 3 recent related meeting notes if overlap is likely

## Speaker identity enrichment
Replace generic labels like `Speaker 1`, `Speaker 2`, `Speaker A`, `Speaker B`, or `Unknown` only with high confidence from:
- transcript metadata
- self-identification
- direct address cues
- matching aliases, handles, or emails in people notes

If confidence is medium:
- ask one bundled clarification with best guesses and short evidence

If confidence is low:
- keep the generic label
- create or update a stub note when helpful
- do not invent generic roles such as customer, vendor, or friend

## Output notes
When appropriate, create or update:
- a meeting note
- people notes
- project or area notes
- durable resource notes

A meeting note may include:
- Summary
- Decisions
- Action items
- Topics
- Transcript
- Generated artifacts

## Post-ingest
After transcript ingestion:
- run the integrity sweep
- ensure transcript references remain correct
- ensure archived transcript links are correct and relative if files were moved
- correct obvious spelling errors in vault entities only when confidence is high