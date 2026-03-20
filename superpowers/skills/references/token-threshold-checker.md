# Token Threshold Checker Reference

## Purpose

This reference defines the token budget thresholds and corresponding actions for the compact mechanism. The checker monitors token usage and triggers appropriate reminders to maintain conversation efficiency.

## Thresholds

| Threshold | Icon | Percentage | Action |
|-----------|------|------------|--------|
| Warning | ⚠️ | 50% | Display compact reminder |
| Critical | 🔴 | 70% | Display compact reminder + suggest state capture |
| Emergency | 🚨 | 85% | Force compact mode + require state capture |

## Reminder Messages

### Warning Level (50%)

```
⚠️ Token Budget: 50% used

Compact reminder active. Continue with:
- Minimal summaries
- Direct responses only
- No bullet lists in conclusions
```

### Critical Level (70%)

```
🔴 Token Budget: 70% used

CRITICAL: Compact mode enforced
- Ultra-minimal responses
- Consider state capture for continuation
- Avoid all non-essential output
```

### Emergency Level (85%)

```
🚨 Token Budget: 85% used

EMERGENCY: State capture required
- Capture current context immediately
- Prepare for conversation reset
- Absolute minimum output only
```

## Integration

### When to Check

Check token usage:
1. After each user message
2. Before generating responses
3. After completing major operations
4. When loading additional context

### How to Check

```bash
# Token usage is available in system context
# Compare current usage against budget threshold
if [ $CURRENT_TOKENS -gt $((BUDGET * 50 / 100)) ]; then
    # Trigger appropriate reminder level
fi
```

## Usage Example

```markdown
<!-- In skill SKILL.md files -->

**Token Budget Reminder:**
⚠️ At 50% budget: Compact responses only
🔴 At 70% budget: Ultra-compact + consider state capture
🚨 At 85% budget: State capture required
```

## State Capture on Critical Threshold

When reaching 70% or 85% thresholds, recommend state capture:

1. **Capture current context**: Save work in progress, decisions made, next steps
2. **Create continuation point**: Document where to resume
3. **Reset conversation**: Start fresh with captured state loaded

This ensures continuity while managing token budget effectively.
