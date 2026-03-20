---
name: behavior-driven-development
description: Applies behavior-driven development principles including Gherkin scenarios and test-driven development. This skill should be used when the user asks to implement features, fix bugs, or when writing executable specifications and tests before writing production code.
user-invocable: false
---

# Behavior-Driven Development (BDD) Skill

This skill provides a comprehensive guide to applying Behavior-Driven Development principles to your coding tasks. BDD is not just about tools; it's a methodology for shared understanding and high-quality implementation.

## How to Use This Skill

When the user asks for a feature, bug fix, or refactor, apply the following mindset:

1.  **Understand Behavior First:** Do not start coding until you know *what* the system should do.
2.  **Define Scenarios:** Create or ask for concrete examples (Gherkin) of the expected behavior.
3.  **Drive Implementation with Tests:** Use the Red-Green-Refactor cycle.

## Core Concepts

### 1. The BDD Cycle
The process flows from requirements to code:
*   **Discovery:** Clarify requirements through examples (The "Three Amigos").
*   **Formulation:** Write these examples as specific scenarios (Given/When/Then).
*   **Automation:** Implement using TDD.

See [BDD Best Practices](./references/bdd-best-practices.md) for a detailed guide.

### 2. Writing Scenarios (Gherkin)
Scenarios are your "Executable Specifications".
*   Keep them declarative (business focus).
*   Avoid technical jargon and UI details.
*   One behavior per scenario.
*   **Store in .feature files, NOT as code comments** - this makes them executable and accessible to non-technical stakeholders.

See [Cucumber Gherkin Guide](./references/gherkin-guide.md) for syntax and storage structure.

### 3. Red-Green-Refactor (TDD)
The engine of implementation:
1.  **RED:** Write a failing test for the scenario (or a unit thereof).
2.  **GREEN:** Write the minimal code to pass the test.
3.  **REFACTOR:** Clean up the code while keeping tests passing.

### 4. Compact Reminder on BDD Retry Threshold

**When to trigger:** When BDD loop shows signs of non-convergence

**Trigger conditions:**
- Same scenario fails 2+ times
- Retry count reaches 50% of threshold (e.g., 4/8)
- Error pattern not converging (same error repeating)

**Reminder message:**

```
🚨 **Compact Reminder - BDD Loop Retry Threshold**

BDD loop showing signs of non-convergence. Consider using `/compact` to preserve context before escalation:
- Scenario: {{SCENARIO_NAME}}
- Retry count: {{RETRY_COUNT}} / {{MAX_RETRIES}}
- Error pattern: {{ERROR_PATTERN}}
- Convergence: {{CONVERGING_STATUS}}

Token usage: {{TOKEN_USAGE}} / {{TOKEN_LIMIT}}

To compact now: `/compact`
To continue: Attempt next retry or escalate
```

**State capture:**
- Use `superpowers/skills/references/compact-state-extractor.md` to extract BDD Loop state
- Use `superpowers/skills/references/compact-template.md` to generate compact document
- Use `superpowers/skills/references/state-json-writer.md` to write `.claude/.superpower-state.json`
- Save compact document to `.claude/compacts/YYYY-MM-DD-HHMMSS.md`

**Escalation signals to capture:**
- Repeated error messages
- Non-converging test failures
- Approaching retry limit

## Quick Reference: The Iron Law

> **"No production code is written without a failing test first."**

If you write code before the test:
1.  You don't know if the test is capable of failing (false positives).
2.  You are biased by your implementation.
3.  You are writing legacy code from day one.
