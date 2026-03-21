# Integrity sweep

Run this after any create, edit, move, or rename.

## Required checks
- Repair changed `[[...]]` and `![[...]]` references.
- Search for dangling references to old titles or paths.
- Check whether summaries, dashboards, project notes, or person notes need propagation updates.
- Check for duplicate tasks introduced by the change.
- Check for ambiguous duplicate titles and resolve with aliases or clearer naming.

## Report
State:
- what was fixed automatically
- what remains unresolved
- what needs human judgment

## Link-safe moves and renames
When moving or renaming a note:

1. Find inbound links and embeds.
2. Prefer alias-first behavior when possible by preserving the old title as an alias.
3. Move or rename the note.
4. Update links and embeds everywhere needed.
5. Verify that no stale references remain.
6. Record the change in an updates or changelog section if the local note pattern uses one.