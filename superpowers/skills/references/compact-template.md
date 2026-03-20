# Compact Document Template

## Purpose

Template for generating complete compact state documents in `.claude/compacts/` or `.codex/compacts/`.

## Template Structure

```markdown
# Compact State - {{TIMESTAMP}}

**Trigger:** {{TRIGGER_REASON}}
**Phase:** {{CURRENT_PHASE}}
**Token Usage:** {{TOKEN_USAGE}} / {{TOKEN_LIMIT}}

## BDD Loop State

### Current Cycle
- **Phase:** {{BDD_PHASE}}
- **Scenario:** {{CURRENT_SCENARIO}}
- **Iteration:** {{ITERATION_COUNT}}
- **Convergence:** {{CONVERGING_STATUS}}

### Loop History

| Scenario | Status | Retries | Error Pattern |
|----------|--------|---------|---------------|
{{LOOP_HISTORY_ROWS}}

### Escalation Signals

{{ESCALATION_SIGNALS_LIST}}

## Agent-Team Loop State

### Team Coordination
- **Mode:** {{TEAM_MODE}}
- **Active Agents:** {{ACTIVE_AGENT_COUNT}}
- **Coordination State:** {{COORDINATION_STATE}}

### Role Status

| Role | Current Task | Status | Blockers |
|------|--------------|--------|----------|
{{ROLE_STATUS_ROWS}}

### Team Loop Signals

{{TEAM_LOOP_SIGNALS_LIST}}

## Workflow State

- **Current Plan:** {{CURRENT_PLAN_PATH}}
- **Current Task:** {{CURRENT_TASK}}
- **Completed:** {{COMPLETED_COUNT}}
- **Remaining:** {{REMAINING_COUNT}}

## Loop Continuation Instructions

### On Resume

{{RESUME_INSTRUCTIONS}}

### Terminal Conditions

{{TERMINAL_CONDITIONS}}

## Session Context

### Key Decisions

{{KEY_DECISIONS_LIST}}

### Important Code Changes

{{CODE_CHANGES_LIST}}

### TODOs

{{TODOS_LIST}}
```

## Variable Definitions

- `{{TIMESTAMP}}`: ISO 8601 format (YYYY-MM-DD HH:MM:SS)
- `{{TRIGGER_REASON}}`: One of: phase-completion, agent-task-completed, bdd-retry-threshold, token-threshold
- `{{CURRENT_PHASE}}`: brainstorming, writing-plans, executing-plans, etc.
- `{{TOKEN_USAGE}}`: Current token count
- `{{TOKEN_LIMIT}}`: Maximum token limit (usually 200000)
- `{{BDD_PHASE}}`: RED, GREEN, or REFACTOR
- `{{CURRENT_SCENARIO}}`: Gherkin scenario being tested
- `{{ITERATION_COUNT}}`: Number of attempts for current scenario
- `{{CONVERGING_STATUS}}`: true/false - is error pattern changing?
- `{{LOOP_HISTORY_ROWS}}`: Table rows of scenario history
- `{{ESCALATION_SIGNALS_LIST}}`: Bullet list of escalation signals
- `{{TEAM_MODE}}`: Serial or Parallel
- `{{ACTIVE_AGENT_COUNT}}`: Number of active agents
- `{{COORDINATION_STATE}}`: Current coordination status
- `{{ROLE_STATUS_ROWS}}`: Table rows of role status
- `{{TEAM_LOOP_SIGNALS_LIST}}`: Bullet list of team signals
- `{{CURRENT_PLAN_PATH}}`: Path to current plan file
- `{{CURRENT_TASK}}`: Current task identifier
- `{{COMPLETED_COUNT}}`: Number of completed tasks
- `{{REMAINING_COUNT}}`: Number of remaining tasks
- `{{RESUME_INSTRUCTIONS}}`: Instructions for resuming work
- `{{TERMINAL_CONDITIONS}}`: Conditions for ending the loop
- `{{KEY_DECISIONS_LIST}}`: Bullet list of key decisions
- `{{CODE_CHANGES_LIST}}`: Bullet list of important code changes
- `{{TODOS_LIST}}`: Bullet list of TODOs

## Usage

Skills should populate this template when generating compact documents.
