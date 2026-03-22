# Link discovery

Use this workflow when manually creating a new note (not via transcript ingestion).

## Discovery behavior

Search the vault for potentially related notes based on:

- Keywords and topics from the new note's title and content
- **Explicit connections**: same project, person, or explicit mentions
- Avoid thematic/implicit connections for auto-linking

The discovery process prioritizes clarity and relevance over quantity. Focus on connections that are directly stated or clearly implied in the content.

## Suggestion rules

- Present 3-5 most relevant suggestions
- Format: "I found these related notes: [[X]], [[Y]], [[Z]]"
- Auto-link only explicit connections:
  - Same project references
  - Person mentions
  - Direct topic matches
- Leave thematic/implicit connections for user to decide

When suggesting links, provide brief context about why each note is relevant (e.g., "both discuss [[Topic X]]" or "related to [[Project Y]]").

## Search scope

Full vault search with the following priority:

1. **Project notes** (Projects/) - Highest priority for project-related content
2. **Person notes** (Resources/People/) - For mentions of individuals
3. **Active area notes** (Areas/) - For ongoing responsibilities
4. **Resource notes** (Resources/) - For reference material

Search should be case-insensitive and consider both title and content matches.

## Conservative approach

Suggest links, never auto-add without explicit user confirmation.

Distinguish between:

- **Explicit**: "Working on [[Project Alpha]]" → auto-link
- **Implicit**: "Similar to [[Project Alpha]]" → suggest only

When in doubt, suggest and let user decide. Never assume connections that aren't clearly stated.

## Idempotency

- Repeated runs produce the same suggestions
- No duplicate link suggestions
- Respect existing links (don't suggest already-linked notes)
- If a link already exists, do not suggest it again

This ensures consistent behavior across multiple editing sessions and prevents suggestion fatigue.
