# AGENTS.md (Obsidian References)

## OVERVIEW
Specialized guidance for Obsidian vault operations. These reference files provide the "how-to" for specific tasks routed by the main Obsidian skill. They ensure consistency across reading, writing, and organization.

## REFERENCE FILES
| File | Purpose | Key Discipline |
|------|---------|----------------|
| `read-workflow.md` | Grounded reading | Search first; build read plans; answer only from notes read. |
| `write-routing.md` | Note creation/edits | Build write plans; minimal patching; delegate to `obsidian-markdown`. |
| `para-routing.md` | Note placement | Projects, Areas, Resources, Archive; meeting and person note paths. |
| `integrity-sweep.md` | Post-change safety | Link repair; alias-first renaming; update propagation; deduplication. |
| `transcript-ingestion.md` | Transcript logic | Speaker ID; note creation; archiving after `/obsidian-ingest`. |
| `task-conventions.md` | Task standards | Checkbox formatting; placement in project/person notes; dedupe logic. |
| `people-style-profile.md` | Style profiling | Evidence-based cognition notes; non-judgmental style observations. |

## HOW THEY FIT TOGETHER
The Obsidian skill (`SKILL.md`) acts as a traffic controller. It analyzes the user's request and loads only the relevant reference file(s) to minimize context overhead.

1. **Analysis**: `SKILL.md` determines the operation type (e.g., "Update a project note").
2. **Loading**: The agent loads `write-routing.md` for the edit and `para-routing.md` to confirm the path.
3. **Execution**: The agent follows the specific workflow (e.g., "Build a write plan first").
4. **Verification**: After the edit, `integrity-sweep.md` is loaded to ensure no links were broken.

## CONVENTIONS
- **Atomic Loading**: Only load what is needed for the current step.
- **Telegraphic Prose**: Keep guidance concise and actionable.
- **No Duplication**: Do not repeat rules found in the parent skill or individual references.
- **Idempotency**: All workflows must produce consistent results on repeated runs.
- **Groundedness**: All actions must be rooted in vault evidence, not memory.

## MAINTENANCE
- Update these references when vault conventions change.
- Ensure new reference files are added to this index and the parent skill.
- Keep individual files focused on a single operational domain.
