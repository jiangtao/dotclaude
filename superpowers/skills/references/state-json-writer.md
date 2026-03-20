# State JSON Writer Reference

## Purpose

This reference provides the specification for generating minimal state JSON files that capture the current session context. These files enable state persistence, handoffs between agents, and workflow resumption.

## JSON Structure

```json
{
  "timestamp": "2026-03-20T14:30:00Z",
  "version": "1.0",
  "sessionContext": {
    "workingDirectory": "/absolute/path/to/project",
    "gitBranch": "feature/branch-name",
    "activeFiles": [
      "/absolute/path/to/file1.ts",
      "/absolute/path/to/file2.md"
    ],
    "recentCommands": [
      "/command:name arg1 arg2",
      "git status"
    ]
  },
  "workflowState": {
    "currentPhase": "implementation",
    "completedSteps": [
      "requirements gathered",
      "design approved"
    ],
    "nextSteps": [
      "implement feature X",
      "write tests"
    ],
    "blockers": []
  },
  "bddLoop": {
    "active": true,
    "currentCycle": 3,
    "redPhase": {
      "testFile": "/path/to/test.spec.ts",
      "failingTests": ["should handle edge case"]
    },
    "greenPhase": {
      "implementationFile": "/path/to/implementation.ts",
      "passingTests": ["should handle basic case"]
    },
    "refactorPhase": {
      "targets": ["extract helper function"],
      "completed": false
    }
  },
  "agentTeam": {
    "activeAgents": [
      {
        "name": "code-reviewer",
        "role": "review",
        "status": "active",
        "assignedFiles": ["/path/to/file.ts"]
      }
    ],
    "handoffQueue": [],
    "sharedContext": {
      "projectGoal": "implement feature X",
      "constraints": ["maintain backward compatibility"]
    }
  }
}
```

## Field Definitions

### Root Level

- `timestamp`: ISO 8601 timestamp of state capture
- `version`: State schema version (currently "1.0")

### sessionContext

Captures the immediate working environment:

- `workingDirectory`: Absolute path to current working directory
- `gitBranch`: Current git branch name (if in git repo)
- `activeFiles`: Array of absolute paths to files currently being worked on
- `recentCommands`: Last 5-10 commands executed (slash commands or shell commands)

### workflowState

Tracks progress through multi-step workflows:

- `currentPhase`: Current workflow phase (e.g., "planning", "implementation", "testing", "review")
- `completedSteps`: Array of completed workflow steps
- `nextSteps`: Array of upcoming steps to execute
- `blockers`: Array of blocking issues (empty if none)

### bddLoop

Tracks BDD/TDD cycle state (omit if not in BDD workflow):

- `active`: Boolean indicating if BDD loop is active
- `currentCycle`: Iteration number
- `redPhase`: Test writing phase
  - `testFile`: Path to test file
  - `failingTests`: Array of test names that should fail
- `greenPhase`: Implementation phase
  - `implementationFile`: Path to implementation file
  - `passingTests`: Array of test names that now pass
- `refactorPhase`: Refactoring phase
  - `targets`: Array of refactoring goals
  - `completed`: Boolean indicating phase completion

### agentTeam

Tracks multi-agent collaboration (omit if single agent):

- `activeAgents`: Array of currently active agents
  - `name`: Agent identifier
  - `role`: Agent's role (e.g., "review", "test", "implement")
  - `status`: Current status ("active", "waiting", "completed")
  - `assignedFiles`: Files assigned to this agent
- `handoffQueue`: Array of pending agent handoffs
- `sharedContext`: Context shared across all agents
  - `projectGoal`: High-level goal
  - `constraints`: Array of constraints or requirements

## Storage Locations

### .claude/ Directory (Session-Scoped)

Store temporary session state in `.claude/state/`:

```
.claude/
└── state/
    ├── current-session.json      # Active session state
    └── session-YYYYMMDD-HHMMSS.json  # Historical sessions
```

These files are gitignored and cleaned up after session completion.

### .codex/ Directory (Project-Scoped)

Store persistent workflow state in `.codex/`:

```
.codex/
├── workflow-state.json           # Current workflow state
└── checkpoints/
    └── checkpoint-YYYYMMDD.json  # Workflow checkpoints
```

These files can be committed to track project progress across sessions.

## Usage

### Minimal State Capture

For simple handoffs, include only essential fields:

```json
{
  "timestamp": "2026-03-20T14:30:00Z",
  "version": "1.0",
  "sessionContext": {
    "workingDirectory": "/path/to/project",
    "activeFiles": ["/path/to/file.ts"]
  },
  "workflowState": {
    "currentPhase": "implementation",
    "nextSteps": ["complete function X"]
  }
}
```

### Full State Capture

For complex workflows with BDD and multi-agent collaboration, include all relevant sections. Omit sections that don't apply to the current workflow.

### State Updates

Update state files at key transition points:
- Before agent handoffs
- After completing workflow phases
- Before/after BDD cycle iterations
- When blockers are encountered or resolved

### State Restoration

When resuming from state:
1. Read the state JSON file
2. Restore working directory context
3. Load active files
4. Resume from `currentPhase` and `nextSteps`
5. Reactivate agents if in multi-agent workflow
