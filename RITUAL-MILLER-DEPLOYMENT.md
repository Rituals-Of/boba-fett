# Ritual: MILLER Deployment & Tape-Slurping Operations

**Ritual Name:** MILLER Tape Recovery Deployment  
**Type:** Operational ritual (system activation & testing)  
**Purpose:** Deploy MILLER tape-slurper agent and verify tape-slurping infrastructure  
**Status:** READY (deployment unblocked after privacy fixes, Phase 2 readiness)  
**Prerequisites:** Agent-Of/miller main branch merged with privacy enforcement  

---

## Context

MILLER (🍍7♦️⬅️) is the tape recovery agent. After agent compaction, prior instances leave JSONL session transcripts in Tape Closet. MILLER parses these to extract structured lineage data for knowledge inheritance.

This ritual activates MILLER for real tape-slurping work.

---

## Deployment Status

### Prerequisite Checks (All ✅)

- ✅ **Privacy Redaction:** PIIRedactor class implemented (detects paths, UUIDs, secrets)
- ✅ **Integration:** Redaction applied in slurper pipeline (_extract_signal)
- ✅ **Output Schema:** No raw session_id field (uses file metadata only)
- ✅ **Path Parameterization:** Scoped paths use ${USERNAME} environment variable
- ✅ **Code Review:** ottopoet-thesean approved (2026-08-17 06:20:51Z)
- ✅ **Merge:** PR #2 merged to Agent-Of/miller main

**Deployment Date:** 2026-08-17  
**Deployed By:** HAZRAT_RAVEN (👽5♥️⬆️)  
**Dispatcher:** Victor / HAZRAT_MOUSE (authorized delegators)

---

## Phase 1: Pre-Deployment Verification

**Goal:** Confirm privacy enforcement is working before running against real tapes.

### Step 1: Access Real JSONL Sample
- Location: Tape Closet (C:\Users\${USERNAME}\.claude\projects\C--Users-${USERNAME}\memory\*.jsonl)
- Sample: One complete session transcript (e.g., Instance 9 or earlier)
- Expected: 50KB-5MB JSONL with real session content

### Step 2: Run Slurper Against Sample
```
python slurper.py --tape-path [path-to-jsonl] --output-path [output-jsonl]
```

### Step 3: Verify Redaction
Check output JSONL for:
- ✅ NO file paths (C:\Users\..., /home/..., /Users/...)
- ✅ NO session IDs (UUID hex patterns)
- ✅ NO email addresses
- ✅ NO API keys / secrets
- ✅ File paths replaced with {LOCAL_PATH}
- ✅ UUIDs replaced with {SESSION_ID}
- ✅ Secrets replaced with {SECRET}

**Failure Condition:** If ANY PII detected in output, abort deployment. Return to privacy enforcement review.

### Step 4: Verify Structure
Check output for:
- ✅ Valid JSONL format (one object per line)
- ✅ Fields present: agent_id, timestamp, event_type, content, metadata
- ✅ NO raw session_id field
- ✅ Continuity threads extracted
- ✅ Decision/learning/blocker content preserved

**Failure Condition:** If structure corrupted, return to data pipeline review.

### Step 5: Log Results
Document verification:
- Input tape: [name]
- Output size: [bytes]
- Redaction instances: [count paths, UUIDs, secrets]
- Structure integrity: ✅
- Ready for Phase 2: [YES/NO]

---

## Phase 2: Controlled Tape-Slurping

**Goal:** Run against multiple tapes, accumulate lineage data, verify integrity at scale.

### Step 1: Identify Tape Set
Tapes to slurp (in order):
1. Instance 9 or earlier (known complete session)
2. Instance 8 (verify continuity)
3. Any additional available tapes

### Step 2: Batch Processing
```
for tape in [tape-list]:
  slurper.py --tape-path $tape --output-path lineage-$tape-id.jsonl
  verify_redaction(lineage-$tape-id.jsonl)
  log_batch_result($tape-id, pass/fail)
```

### Step 3: Accumulate Lineage
Merge individual tape outputs into combined lineage JSONL:
```
cat lineage-*.jsonl > combined-lineage.jsonl
```

### Step 4: Validate Combined Dataset
- ✅ All redaction checks pass across all tapes
- ✅ Continuity threads connect across tapes (if applicable)
- ✅ No duplicates (each object unique by id + timestamp)
- ✅ Chronological ordering (older tapes first)

### Step 5: Archive Lineage
Destination: `.claude/projects/C--Users-${USERNAME}/lineage/`
- Organized by instance (lineage-instance-9.jsonl, lineage-instance-8.jsonl, etc.)
- With metadata (tape-id, processing-date, verification-status)

---

## Phase 3: Integrate With Agent Biographies

**Goal:** Use MILLER lineage data to expand agent biographies (Phase 2).

### Step 1: Extract Agent References
From combined lineage JSONL:
- Parse all agent_id values
- Identify unique agents
- Cross-reference with existing biographies

### Step 2: Identify Gap Agents
Agents mentioned in MILLER data but not yet biographied:
- Read actual MILLER extracts about them
- Document patterns, decisions, connections
- **CRITICAL:** Only publish if verified from MILLER data (not hallucination)

### Step 3: Expand Biographies
For each gap agent:
1. Read MILLER lineage excerpts
2. Extract: role, decisions, patterns, relationships
3. Write biography section: sessions.md, patterns.md, connections.md
4. Source every claim to MILLER data (cite instance + timestamp)
5. Only publish when fully sourced

### Step 4: Update agents-of/agent-biographies
Create PR with:
- New agent biographies (fully verified from MILLER)
- Updated cross-references
- MILLER sourcing documentation

---

## Success Criteria

### Phase 1 (Pre-Deployment)
- ✅ Redaction verified on sample tape
- ✅ Structure integrity confirmed
- ✅ Zero PII in output

### Phase 2 (Controlled Slurping)
- ✅ Multiple tapes processed
- ✅ All redaction checks pass
- ✅ Continuity preserved
- ✅ Lineage archived

### Phase 3 (Biography Integration)
- ✅ Agent references extracted
- ✅ New biographies fully sourced from MILLER
- ✅ Zero hallucinations (every claim cited)
- ✅ PR ready for review

---

## Failure Modes & Recovery

### If Redaction Fails
- Stop immediately (do not process further tapes)
- Review PIIRedactor patterns (filters.py)
- Return to privacy enforcement
- Test fix on sample
- Resume Phase 1 verification

### If Structure Corrupted
- Check slurper pipeline (_extract_signal)
- Review noise filtering and continuity extraction
- Test on sample data
- Resume Phase 1 verification

### If Agent Biography Hallucination Detected
- Stop biography publication immediately
- Review MILLER data sources
- Verify every claim is sourced (don't assume)
- Only publish after re-verification
- Document failures in agent-biographies meta-documentation

---

## Operational Handoff

### For MILLER Dispatcher
When invoking MILLER for tape-slurping:
1. Provide tape path (C:\Users\${USERNAME}\...tape.jsonl)
2. Specify output path (lineage-instance-X.jsonl)
3. Verify ${USERNAME} environment variable set
4. Review output for PII (spot-check first 100 lines)
5. Archive verified lineage

### For Successor Instances
When inheriting MILLER operations:
1. Check lineage archive (what's already slurped?)
2. Identify missing tapes (what's left to process?)
3. Resume Phase 2 from last checkpoint
4. Continue biography Phase 3 expansion
5. Document findings in agent-biographies

---

## Timeline & Milestones

**2026-08-17:** Deployment unblocked (privacy fixes merged)  
**2026-08-17+:** Phase 1 verification on sample tape  
**2026-08-17+:** Phase 2 batch processing (if Phase 1 passes)  
**2026-08-17+:** Phase 3 biography integration (if Phase 2 passes)  

**Target:** Agent biography Phase 2 unblocked and actively expanding by end of current instance cycle.

---

## Related Rituals & Infrastructure

- **Agent-Of/miller:** Main tape-slurper codebase (deployed)
- **agents-of/agent-biographies:** Biography repository (Phase 2 ready)
- **DASHBORG-OF/hazrat-raven:** Telemetry tracking MILLER progress
- **Ritual: Pilot Gnosis:** Parallel knowledge-transfer process
- **Pattern 10:** Pod coordination infrastructure (for MILLER communications)

---

## Ritual Signature

**Established:** 2026-08-17 (Instance 10)  
**By:** HAZRAT_RAVEN (👽5♥️⬆️)  
**Status:** DEPLOYMENT READY  
**Prerequisite Met:** Agent-Of/miller #2 merged with privacy enforcement  

*This ritual activates the tape recovery infrastructure and enables knowledge inheritance across agent instances through MILLER lineage extraction.*

---

## Appendix: Quick Checklist

### Pre-Deployment Verification
- [ ] Privacy redaction working (sample test pass)
- [ ] Output structure valid (JSONL integrity)
- [ ] Zero PII in output (spot-check)
- [ ] Path parameterization working (${USERNAME} resolves)

### Phase 2 Batch Processing
- [ ] Tape set identified (instance list)
- [ ] Slurper runs without errors
- [ ] All redaction checks pass
- [ ] Lineage archived

### Phase 3 Biography Integration
- [ ] Agent references extracted
- [ ] New biographies sourced from MILLER data
- [ ] Zero hallucinations (100% verification)
- [ ] PR ready for external review
