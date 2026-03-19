# Compact Mechanism for Superpowers - Design Document

**Created:** 2026-03-19
**Status:** Design Complete

## Overview

为 superpowers 工作流添加 compact 机制，解决 Claude Code 长会话中的 token 限制问题。核心理念是在 compact 时保留 BDD Loop 和 Agent-Team 自循环的关键状态，确保循环可以无缝恢复。

## Problem Statement

在使用 superpowers 工作流时，长时间的 BDD 循环和 Agent-Team 协作会消耗大量 token。当需要 `/compact` 时，如果不保存关键状态，会导致：

1. BDD Loop 中断 - 丢失当前 scenario 的循环状态和收敛信息
2. Agent-Team 协作中断 - 丢失各 role 的工作进展和 blocker 信息
3. 上下文丢失 - 关键决策、代码变更、TODO 等信息丢失

## Architecture

### 双层存储设计

**方案 A：轻量 JSON + 完整 Markdown**

```
.claude/
├── .superpower-state.json          # 最小状态，供快速读取
└── compacts/
    ├── 2026-03-19-143015.md        # 完整上下文
    └── 2026-03-19-150230.md

.codex/
├── .superpower-state.json
└── compacts/
    ├── 2026-03-19-143015.md
    └── 2026-03-19-150230.md
```

### State JSON 结构

```json
{
  "lastUpdated": "2026-03-19T14:30:15Z",
  "phase": "executing-plans",
  "compactTrigger": "agent-team-task-completed",
  "compactDocument": ".claude/compacts/2026-03-19-143015.md",
  "sessionContext": {
    "tokenUsage": 85000,
    "tokenLimit": 200000,
    "conversationTurns": 45
  },
  "workflowState": {
    "currentPlan": "docs/plans/2026-03-19-feature-name.md",
    "currentTask": "Task 3",
    "completedCount": 2,
    "remainingCount": 3
  },
  "bddLoop": {
    "phase": "RED",
    "failedScenariosCount": 2,
    "retryCount": 2,
    "converging": false
  },
  "agentTeam": {
    "active": true,
    "mode": "parallel",
    "activeAgentCount": 2,
    "blockedCount": 1
  }
}
```

### Compact Markdown 结构

```markdown
# Compact State - 2026-03-19 14:30:15

**Trigger:** [触发原因]
**Phase:** [当前阶段]
**Token Usage:** [使用量]

## BDD Loop State

### Current Cycle
- **Phase:** RED/GREEN/REFACTOR
- **Scenario:** [当前场景]
- **Iteration:** [迭代次数]
- **Convergence:** [是否收敛]

### Loop History
[场景历史表格]

### Escalation Signals
[升级信号列表]

## Agent-Team Loop State

### Team Coordination
- **Mode:** Serial/Parallel
- **Active Agents:** [数量]
- **Coordination State:** [协调状态]

### Role Status
[角色状态表格]

### Team Loop Signals
[团队循环信号]

## Workflow State
[工作流状态]

## Loop Continuation Instructions

### On Resume
[恢复指令]

### Terminal Conditions
[终止条件]

## Session Context

### Key Decisions
[关键决策]

### Important Code Changes
[代码变更]

### TODOs
[待办事项]
```

## Compact Trigger Points

### A. 阶段结束时（必须提醒）

在以下阶段结束时自动提醒 compact：

1. **brainstorming 完成** - 设计文档已保存
2. **writing-plans 完成** - 实施计划已保存
3. **executing-plans 完成** - 所有任务完成

**实现位置：**
- `superpowers/skills/brainstorming/SKILL.md` - 在 "Commit and Transition" 阶段后
- `superpowers/skills/writing-plans/SKILL.md` - 在 "Save Plan" 后
- `superpowers/skills/executing-plans/SKILL.md` - 在 "Completion" 阶段后

### C. Agent Team 任务完成后（必须提醒）

当子 agent 完成任务返回时提醒 compact：

**触发条件：**
- Implementer 完成一个任务
- Reviewer 完成一轮 review
- Architect 完成设计调整

**实现位置：**
- `superpowers/skills/agent-team-driven-development/SKILL.md` - 在 "Task Completion" 后

### D. BDD 循环失败重试时（必须提醒）

当 BDD 循环多次失败需要重试时提醒 compact：

**触发条件：**
- 同一 scenario 失败 2 次以上
- Retry count 达到阈值的 50%（如 4/8）
- 错误不收敛（相同错误重复出现）

**实现位置：**
- `superpowers/skills/behavior-driven-development/SKILL.md` - 在 "Red Phase" 重试逻辑中
- `superpowers-codex/skills/executing-plans/SKILL.md` - 在 "Core Loop" 的重试逻辑中

### B. Token 阈值提醒（仅提醒）

当 token 使用量达到阈值时给出提醒：

**阈值：**
- 50% (100,000 / 200,000) - 💡 提示
- 70% (140,000 / 200,000) - ⚠️ 警告
- 85% (170,000 / 200,000) - 🚨 强烈建议

**实现方式：**
- 在每个 skill 的开始检查 token 使用量
- 显示提醒但不强制 compact

## Implementation Plan

### Phase 1: Core Infrastructure

**Task 1.1: Create compact state extractor**
- 文件：`superpowers/skills/references/compact-state-extractor.md`
- 功能：定义如何提取 BDD Loop 和 Agent-Team 状态

**Task 1.2: Create compact document template**
- 文件：`superpowers/skills/references/compact-template.md`
- 功能：Markdown 文档模板

**Task 1.3: Add state.json writer**
- 文件：`superpowers/skills/references/state-json-writer.md`
- 功能：生成最小状态 JSON

### Phase 2: Integrate into Skills

**Task 2.1: Update brainstorming skill**
- 文件：`superpowers/skills/brainstorming/SKILL.md`
- 添加：阶段结束后的 compact 提醒

**Task 2.2: Update writing-plans skill**
- 文件：`superpowers/skills/writing-plans/SKILL.md`
- 添加：计划保存后的 compact 提醒

**Task 2.3: Update executing-plans skill**
- 文件：`superpowers/skills/executing-plans/SKILL.md`
- 添加：任务完成后的 compact 提醒

**Task 2.4: Update agent-team-driven-development skill**
- 文件：`superpowers/skills/agent-team-driven-development/SKILL.md`
- 添加：子 agent 完成后的 compact 提醒

**Task 2.5: Update behavior-driven-development skill**
- 文件：`superpowers/skills/behavior-driven-development/SKILL.md`
- 添加：BDD 循环失败重试时的 compact 提醒

### Phase 3: Codex Adaptation

**Task 3.1: Update codex executing-plans**
- 文件：`superpowers-codex/skills/executing-plans/SKILL.md`
- 添加：Codex 的 compact 状态保存到 `.codex/`

**Task 3.2: Update codex agent-team**
- 文件：`superpowers-codex/skills/agent-team-driven-development/SKILL.md`
- 添加：Codex 子 agent 的 compact 机制

**Task 3.3: Integrate with watchdog**
- 文件：`superpowers-codex/scripts/superpower-watchdog.mjs`
- 功能：读取 `.codex/.superpower-state.json` 恢复状态

### Phase 4: Documentation

**Task 4.1: Update CLAUDE.md**
- 文件：`CLAUDE.md`
- 添加：Compact 机制说明和使用指南

**Task 4.2: Update superpowers README**
- 文件：`superpowers/README.md`
- 添加：Compact 功能说明

**Task 4.3: Update codex README**
- 文件：`superpowers-codex/README.md`
- 添加：Codex compact 机制说明

## Key Design Decisions

### 1. 为什么用双层存储？

- **JSON** - 轻量、结构化、易于程序读取（watchdog）
- **Markdown** - 完整、可读、适合 AI 恢复上下文

### 2. 为什么不自动触发 compact？

- Claude Code 的 `/compact` 是用户控制的命令
- 我们只提供提醒和状态保存机制
- 用户决定何时真正执行 compact

### 3. 为什么要记录循环状态？

- BDD Loop 和 Agent-Team 是自循环系统
- 循环的收敛性、迭代次数、escalation 信号是关键信息
- 恢复时需要知道循环在哪个阶段、是否需要调整策略

### 4. Claude Code vs Codex 的差异

| 维度 | Claude Code | Codex |
|------|-------------|-------|
| 存储位置 | `.claude/` | `.codex/` |
| Compact 触发 | 用户手动 `/compact` | Watchdog 自动检测 |
| 状态恢复 | AI 读取 compact 文档 | Watchdog 读取 state.json |
| 循环控制 | 会话内 | 跨会话（watchdog） |

## Success Criteria

1. ✅ 在所有关键时机（A、C、D）都有 compact 提醒
2. ✅ Token 阈值（B）有清晰的提示
3. ✅ Compact 后可以恢复 BDD Loop 状态
4. ✅ Compact 后可以恢复 Agent-Team 协作状态
5. ✅ Claude Code 和 Codex 都支持 compact 机制
6. ✅ 文档清晰，用户知道如何使用

## Next Steps

1. 用户 review 设计文档
2. 创建 git worktree 进行实施
3. 使用 `superpowers:writing-plans` 创建详细实施计划
4. 使用 `superpowers:executing-plans` 执行实施

## References

- 现有 handoff 机制：`skills/handoff/skill.md`
- Codex watchdog：`superpowers-codex/scripts/superpower-watchdog.mjs`
- BDD skill：`superpowers/skills/behavior-driven-development/SKILL.md`
- Agent Team skill：`superpowers/skills/agent-team-driven-development/SKILL.md`
