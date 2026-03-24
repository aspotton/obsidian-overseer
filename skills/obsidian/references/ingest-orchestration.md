# Ingest Orchestration

Orchestration logic for parallel transcript processing. Coordinates read/write/integrity subagents during `/obsidian-ingest` execution.

## Orchestrator Responsibilities

The orchestrator manages the complete ingestion workflow:

1. **Discovery Phase**: Scan intake folders (`Inbox/Transcripts/` or `Inbox/Transcriptions/`) for unprocessed transcripts
2. **Planning Phase**: Build ingestion plan with metadata for each transcript
3. **Read Phase**: Dispatch parallel Read Subagents for transcript analysis
4. **Write Phase**: Dispatch parallel Write Subagents for note creation/updates
5. **Integrity Phase**: Run integrity sweeps on processed groups
6. **Archive Phase**: Move processed transcripts to `Archive/Transcripts/`
7. **Reporting**: Generate summary of successes, failures, and pending items

## Parallel Processing Rules

### When to Parallelize

**Safe to Parallelize:**
- Reading different transcripts (no shared context)
- Writing to independent notes (no file overlap)
- Integrity checks on separate note groups
- Archiving processed transcripts

**Cannot Parallelize:**
- Multiple writes to the same target file
- Writes that depend on previous writes completing
- Integrity checks that reference ongoing writes

### Concurrency Limits

| Operation | Max Concurrent | Rationale |
|-----------|----------------|-----------|
| Read Subagents | 5 | Prevent context overload |
| Write Subagents | 5 | Prevent file lock conflicts |
| Integrity Checks | 5 | Group by transcript batch |
| Archive Operations | 5 | File system limits |

### Batching Strategy

```
If transcripts > max_concurrency:
  1. Split into batches of size max_concurrency
  2. Process each batch through all phases
  3. Sync barrier between batches
  4. Continue to next batch
```

**Example: 12 transcripts with max_concurrency=5**
- Batch 1: Transcripts 1-5 → Read → Write → Integrity → Archive
- Batch 2: Transcripts 6-10 → Read → Write → Integrity → Archive
- Batch 3: Transcripts 11-12 → Read → Write → Integrity → Archive

## Synchronization Points

### Sync Point 1: Read Completion

```
┌─────────────────────────────────────────────────────────────┐
│ Read Subagents: [1] [2] [3] [4] [5]                        │
│                           │                                  │
│                           ▼                                  │
│                    ┌──────────┐                             │
│                    │  BARRIER │ Wait for ALL reads          │
│                    │  POINT 1 │ or timeout (60s)            │
│                    └────┬─────┘                             │
│                         │                                    │
│                         ▼                                    │
│              Collect all analyses                            │
│              Handle failed reads (log + skip)                │
│              Proceed to Write Phase                          │
└─────────────────────────────────────────────────────────────┘
```

**Timeout Behavior:**
- If timeout occurs, proceed with completed reads
- Log failed reads with transcript paths
- Skip write operations for failed reads

### Sync Point 2: Write Completion

```
┌─────────────────────────────────────────────────────────────┐
│ Write Subagents: [1] [2] [3] [4] [5]                       │
│                           │                                  │
│                           ▼                                  │
│                    ┌──────────┐                             │
│                    │  BARRIER │ Wait for ALL writes         │
│                    │  POINT 2 │ or timeout (90s)            │
│                    └────┬─────┘                             │
│                         │                                    │
│                         ▼                                    │
│              Verify write successes                          │
│              Log failures for manual review                  │
│              Proceed to Integrity Phase                      │
└─────────────────────────────────────────────────────────────┘
```

**Retry Policy:**
- Retry failed writes once with same subagent
- If still fails, mark as pending
- Continue with other writes
- Report partial success in final summary

### Sync Point 3: Integrity Completion

```
┌─────────────────────────────────────────────────────────────┐
│ Integrity Checks: [1] [2] [3] [4] [5]                      │
│                           │                                  │
│                           ▼                                  │
│                    ┌──────────┐                             │
│                    │  BARRIER │ Wait for ALL checks         │
│                    │  POINT 3 │ or timeout (30s)            │
│                    └────┬─────┘                             │
│                         │                                    │
│                         ▼                                    │
│              Generate integrity report                       │
│              Flag issues for manual review                   │
│              Proceed to Archive Phase                        │
└─────────────────────────────────────────────────────────────┘
```

## Error Handling Strategy

### Failure Scenarios

**1. Single Read Subagent Failure**
```
Scenario: Read 3 fails (transcript unreadable)

Action:
  - Log error with transcript path and reason
  - Continue with remaining reads
  - Skip write for failed transcript
  - Report failure in final summary
  - Do NOT halt entire orchestration
```

**2. Single Write Subagent Failure**
```
Scenario: Write 2 fails (note creation error)

Action:
  - Retry once with same subagent
  - If still fails, mark as pending
  - Continue with other writes
  - Report partial success
  - Log details for manual intervention
```

**3. Integrity Sweep Failure**
```
Scenario: Link repair fails for multiple notes

Action:
  - Identify affected notes
  - Generate manual fix report
  - Do NOT rollback successful writes
  - Flag for human review
  - Continue to archive phase
```

**4. Timeout Handling**
```
Scenario: Subagent exceeds timeout

Action:
  - Terminate subagent
  - Check if partial results exist
  - If yes, use partial results
  - If no, mark as failed
  - Continue orchestration
```

### Rollback Policy

**CRITICAL: NEVER rollback successful writes**

- Successful operations remain committed
- Failures logged for manual review
- Maintain audit trail of partial completions
- No atomic transactions across subagents

## File Conflict Prevention

### Conflict Detection

Before dispatching write subagents, analyze target files:

```
writes_by_file = {
  "Projects/Atlas/README.md": [subagent_1, subagent_3],
  "Resources/People/John Smith.md": [subagent_2],
  "Areas/Product/Notes.md": [subagent_4, subagent_5]
}
```

### Mitigation Strategies

**CRITICAL Risk (Same file, multiple writers):**
```
1. Group writes by target file
2. Sequential processing within group
3. Merge changes if multiple subagents target same file
4. Flag conflicts for manual resolution
```

**HIGH Risk (Same person note updated):**
```
1. Queue writes to same person note
2. Sequential application with merge
3. Preserve all contributions
4. Update timestamp and source references
```

**MEDIUM Risk (Note creation vs. reference):**
```
1. Create all new notes first
2. Then process references
3. Retry failed links after creation phase
```

**LOW Risk (Independent notes):**
```
- No special handling needed
- Full parallel processing safe
```

### Logical File Locking

```
Before Write Subagent touches a file:

  lock_file(file_path)
  try:
    perform_write()
  finally:
    unlock_file(file_path)
```

## Phase-by-Phase Guidelines

### Phase 1: Discovery

**Input:** Intake folder path (`Inbox/Transcripts/` or `Inbox/Transcriptions/`)

**Actions:**
1. List all transcript files in intake folder
2. Filter for unprocessed files (not in Archive)
3. Sort by date (oldest first for FIFO processing)
4. Extract metadata for each transcript:
   - File path
   - Date/time
   - Duration (if available)
   - Initial participant list

**Output:** Ingestion plan with transcript metadata

### Phase 2: Parallel Read

**Input:** Ingestion plan from Phase 1

**Actions:**
1. Dispatch Read Subagents (max 5 concurrent)
2. Each subagent receives:
   - Transcript path
   - Related project notes (if known)
   - Related people notes (if known)
   - Analysis requirements (speakers, topics, decisions, action items)
3. Wait at Sync Point 1 (timeout: 60s)
4. Collect all analyses

**Output:** Array of transcript analyses ready for writing

### Phase 3: Parallel Write

**Input:** Transcript analyses from Phase 2

**Actions:**
1. Group writes by target file to prevent conflicts
2. Dispatch Write Subagents (max 5 concurrent)
3. Each subagent receives:
   - Target note path and operation (create/update)
   - Content sections (summary, decisions, action items, etc.)
   - Related notes to link
4. Apply file locking for shared targets
5. Wait at Sync Point 2 (timeout: 90s)
6. Collect all write results

**Output:** Write completion report with successes/failures

### Phase 4: Integrity Sweep

**Input:** Write completion report

**Actions:**
1. Group notes by transcript batch
2. Run integrity checks (max 5 concurrent):
   - Repair broken wikilinks
   - Check for dangling references
   - Verify task propagation
   - Check for duplicates
3. Wait at Sync Point 3 (timeout: 30s)
4. Generate integrity report

**Output:** Integrity sweep report with fixes applied and issues flagged

### Phase 5: Archive

**Input:** Integrity sweep report

**Actions:**
1. Identify successfully processed transcripts
2. Move to `Archive/Transcripts/` preserving directory structure
3. Verify links remain correct after move
4. Update any archive references if needed

**Output:** Archive completion status

### Phase 6: Reporting

**Input:** All phase results

**Output:** Final ingestion report containing:
- Total transcripts processed
- Successful completions
- Failed operations (with reasons)
- Pending items (requiring manual review)
- Integrity issues flagged
- Time elapsed

## Performance Expectations

| Batch Size | Expected Time | Notes |
|------------|---------------|-------|
| 1 transcript | 30-40s | Single subagent path |
| 5 transcripts | 45-60s | Full parallelization |
| 10 transcripts | 60-90s | Two batches of 5 |
| 20 transcripts | 2-3 min | Four batches of 5 |

## Integration Points

- **Read Workflow**: [[read-workflow.md]] - Grounded reading discipline
- **Write Routing**: [[write-routing.md]] - Note creation/editing rules
- **Transcript Ingestion**: [[transcript-ingestion.md]] - Transcript-specific logic
- **Integrity Sweep**: [[integrity-sweep.md]] - Post-write safety checks
- **PARA Routing**: [[para-routing.md]] - Note placement rules
