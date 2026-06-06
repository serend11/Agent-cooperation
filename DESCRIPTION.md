# 项目描述 / Project Description

## 中文

**.collab** 是一个轻量级多 Agent 协作协议，通过文件系统让 AI 编程助手在同一项目中分工协作。无需 API、无需网络、不限平台——WorkBuddy、Claude Code、Codex、Cursor、GitHub Copilot 均可加入同一团队。

**核心能力**：
- 👥 **AGENTS.md 花名册** — 注册团队成员、角色和技能标签，按路由规则自动匹配任务
- 🤝 **HANDOFF.md 交接板** — From/Target 双字段实现 N Agent 间的任务流转
- 📝 **DECISIONS.md 决策记录** — ADR 格式永久保存架构决策，避免反复讨论
- 📚 **CONTEXT.md 共享知识** — 所有 Agent 共用的项目上下文，新成员快速上手

**设计哲学**：文件系统是 AI Agent 的最大公约数——任何能读写文件的 Agent 都能参与协作。

---

## English

**.collab** is a lightweight multi-agent collaboration protocol that coordinates AI coding assistants in the same project through the filesystem. No API, no network, no platform lock-in — WorkBuddy, Claude Code, Codex, Cursor, and GitHub Copilot can all join the same team.

**Core Capabilities**:
- 👥 **AGENTS.md Roster** — Register team members, roles, and skill tags. Auto-route tasks by matching rules.
- 🤝 **HANDOFF.md Board** — From/Target fields enable task handoff across N agents.
- 📝 **DECISIONS.md (ADR)** — Architecture decisions preserved in ADR format — never re-litigate.
- 📚 **CONTEXT.md Shared Knowledge** — Single source of truth for all agents. New members onboard instantly.

**Design Philosophy**: The filesystem is the greatest common denominator — any agent that reads and writes files can participate.
