# Write and edit workflow

Use this workflow for creating, updating, appending to, refactoring, moving, or renaming Obsidian notes.

## Build a write plan first

Before any create, edit, move, or rename, create a Write Plan.

Include:
- files to create, update, move, or rename
- exact sections or regions to patch when known
- why each change is needed
- any ambiguity that could cause wrong filing, linking, or identity resolution

## Writing behavior

For any task that writes or edits Obsidian markdown files:

- Use the `obsidian-markdown` skill for note-writing behavior if that skill is available.
- Do not duplicate general Obsidian markdown formatting rules here.
- Use this skill's references only for vault-specific routing, naming, metadata, linking, and update discipline.

## Editing principles

- Patch the smallest relevant section.
- Do not delete user content unless explicitly requested or clearly superseded.
- Preserve structure where possible.
- Keep repeated runs idempotent.
- Avoid introducing duplicate sections, tasks, or backlinks.

## Link discovery for new notes
When creating a NEW note (not via transcript ingestion):

1. After drafting initial content, consult `link-discovery.md`
2. Search for potentially related notes based on keywords/topics
3. Present suggestions to user:
   "I found these related notes: [[X]], [[Y]], [[Z]]"
4. Auto-link only explicit connections:
   - Same project references
   - Person mentions
   - Direct topic matches
5. Leave thematic/implicit connections for user to decide

When to skip link discovery:
- User explicitly disables it
- Note is in Inbox/Temp folder
- Transcript ingestion (handled separately by transcript-ingestion.md)

## When to load more context

- Load `para-routing.md` if note placement or note type matters.
- Load `task-conventions.md` if tasks are being created or updated.
- Load `integrity-sweep.md` after any write.
- Load `people-style-profile.md` only if a person note or style-based drafting task is involved.

## Fallback
If `obsidian-markdown` is unavailable, preserve existing note structure and write standard Obsidian-compatible markdown using only the vault-specific conventions in this skill.