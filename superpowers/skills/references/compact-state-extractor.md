# Compact State Extractor

## Purpose

Extract BDD Loop and Agent-Team state for compact operations to enable seamless recovery after `/compact`.

## State Extraction Functions

### Extract BDD Loop State

When in a BDD cycle, capture:

**Current Cycle Information:**
- Phase: RED/GREEN/REFACTOR
- Current scenario being tested
- Iteration count for this scenario
- Convergence status (is error pattern repeating?)

**Loop History:**
- List of scenarios attempted
- Pass/fail status for each
- Error patterns observed
- Retry counts

**Escalation Signals:**
- Same error appearing multiple times
- Retry count approaching threshold
- Non-converging error patterns

### Extract Agent-Team State

When Agent Team is active, capture:

**Team Coordination:**
- Mode: Serial or Parallel
- Number of active agents
- Current coordination state

**Role Status:**
- Implementer: current task, status, blockers
- Reviewer: review queue, findings, status
- Architect: design decisions, adjustments, status

**Team Loop Signals:**
- Blocked agents count
- Pending reviews
- Design adjustment requests

## Usage in Skills

Skills should call these extraction functions when:
1. Phase completion (brainstorming, writing-plans, executing-plans)
2. Agent task completion
3. BDD retry threshold reached
4. Token usage exceeds 85%

## Output Format

Extracted state should be formatted as:
- JSON for `.superpower-state.json` (minimal, structured)
- Markdown for `.claude/compacts/YYYY-MM-DD-HHMMSS.md` (complete, readable)
