# .collab — 多 Agent 协作协议

> AI 编程助手之间的文件系统级协作框架。任何能读写文件的 Agent 都能加入。

<p align="center">
  <img src="https://img.shields.io/badge/version-2.0.0-green" alt="version">
  <img src="https://img.shields.io/badge/license-MIT-blue" alt="license">
  <img src="https://img.shields.io/badge/platform-any-orange" alt="platform">
</p>

---

## 是什么

`.collab/` 是一个轻量级协作协议，让 **任意多个 AI 编程助手** 在同一项目中高效协作，无需共享内存、无需 API、无需同平台。

你可以在一个项目里同时使用 WorkBuddy、Claude Code、Codex、Cursor、GitHub Copilot，它们通过 `.collab/` 目录中的文件各司其职。

### 解决什么问题

| 问题 | .collab 方案 |
|------|-------------|
| 多个 AI 助手互相不知道对方做了什么 | 共享 CONTEXT.md + HANDOFF.md |
| 换一个助手就要重新解释项目 | AGENTS.md 花名册 + 角色分工 |
| 重大技术决策反复讨论 | ADR 格式的 DECISIONS.md |
| 不知道某个任务该交给谁 | 路由规则自动匹配 |

---

## 工作原理

```
┌──────────────────────────────────────────────────┐
│                   .collab/                        │
│                                                   │
│  AGENTS.md   ← 谁在团队里？什么角色？             │
│  HANDOFF.md  ← 当前任务在谁手上？下一步谁接？     │
│  CONTEXT.md  ← 项目知识共享（所有 Agent 读写）    │
│  DECISIONS.md← 架构决策记录（永不丢失的上下文）   │
│  ROLES.md    ← 预定义角色模板参考库               │
│                                                   │
└──────────────────────────────────────────────────┘
         ▲          ▲          ▲
         │          │          │
    ┌────┴───┐ ┌───┴────┐ ┌───┴────┐
    │Alice   │ │Bob     │ │Carol   │
    │WorkBuddy│ │Claude  │ │Codex   │
    │前端实现  │ │后端API  │ │代码审查 │
    └────────┘ └────────┘ └────────┘
```

---

## 快速上手（2 分钟）

### 1. 安装 Skill

每个 Agent 平台装一份：

**WorkBuddy**:
```bash
mkdir -p ~/.workbuddy/skills/collab
cp SKILL.md ~/.workbuddy/skills/collab/
```

**Claude Code**:
```bash
mkdir -p ~/.claude/skills/collab
cp SKILL.md ~/.claude/skills/collab/
```

**Codex / Cursor / 其他**:
```bash
# 把 SKILL.md 放到该平台的 skill 目录即可
```

### 2. 初始化团队

在任何项目中，告诉任意一个 Agent：

> "初始化 collab 团队"

Agent 会问你 4 个问题，然后自动创建 `.collab/` 目录和所有文件。

### 3. 开始协作

日常使用命令：

| 命令 | 干什么 |
|------|--------|
| `team` | 看团队有哪些人 |
| `status` | 协作状态总览 |
| `handoff bob` | 把当前任务交给 bob |
| `takeover` | 接手分配给自己的任务 |
| `assign alice 改一下登录页` | 给 alice 派活 |
| `context update` | 记录重要发现 |
| `decide 选择 React 而非 Vue` | 记录架构决策 |

---

## 命令速查

```
team                    查看团队花名册
status                  协作仪表盘（团队 + 任务 + 决策 + 上下文）
handoff <agent>         将当前任务交接给指定 Agent
takeover                接手分配给自己的任务
assign <agent> <task>   给其他 Agent 派任务
context [show|update]   查看/更新共享知识库
decide <title>          记录架构决策 (ADR)
join                    加入现有团队
leave                   离开团队
```

---

## 文件结构

```
my-project/
├── .collab/
│   ├── AGENTS.md       ← 团队花名册 + 路由规则
│   ├── HANDOFF.md      ← 当前任务交接状态
│   ├── CONTEXT.md      ← 共享项目知识
│   ├── DECISIONS.md    ← 架构决策记录
│   └── ROLES.md        ← 角色模板参考
├── src/
├── ...
└── .gitignore           ← 建议把 .collab/ 纳入版本控制
```

---

## 为什么用文件系统而不是 API？

| 特性 | 文件系统方案 | API 方案 |
|------|:----------:|:------:|
| 零依赖 | ✅ | ❌ 需要服务端 |
| 离线可用 | ✅ | ❌ |
| Git 版本控制 | ✅ | ❌ |
| 任何 Agent 都能用 | ✅ | 看 API 支持 |
| 可审计 | ✅ | 看实现 |
| 实时性 | ❌ 需手动读取 | ✅ 实时推送 |

**设计哲学**：简单 > 完美。文件系统是 AI Agent 的最大公约数——所有 Agent 都能读文件、写文件。

---

## 常见协作模式

### 双人转：实现 ↔ 审查
```
Alice (实现) → handoff → Bob (审查) → handoff → Alice (修复) → ✅
```

### 三人行：前端 + 后端 + QA
```
Alice (前端) ─┐
              ├→ Carol (QA) → 修复 → Carol (确认) → ✅
Bob (后端)  ─┘
```

### 专家池：按技能路由
```
用户提需求 → 查 AGENTS.md → 找到最匹配的 Agent → assign
```

### 独狼 + 外援：平时独立，遇到问题搬救兵
```
你 → 遇到安全审查 → handoff Bob → Bob 审完 → takeover → 继续
```

---

## 从 v1 升级

如果你之前用 v1（WorkBuddy ↔ Reasonix 专用版）：

1. 替换 SKILL.md
2. 创建 `templates/AGENTS.md`（新增）
3. 把旧 ROLES.md 中的固定角色信息迁移到 AGENTS.md 的 Roster 表格
4. 更新 HANDOFF.md 格式（新增 `From` / `Target` 字段）
5. 更新后所有 Agent 重启会话即可

---

## FAQ

**Q: 多个 Agent 能同时工作吗？**
A: 不推荐同时编辑同一文件。通过 HANDOFF.md 串行协作，或严格划分文件职责。

**Q: Agent 离线了怎么接任务？**
A: 任务留在 HANDOFF.md，状态 `⏳ 等待接手`。下次启动自动发现。

**Q: 需要所有 Agent 用同一个 AI 模型吗？**
A: 不需要。Claude、GPT、Gemini、Qwen 都可以——只要它能读文件、写文件。

**Q: `.collab/` 应该放 .gitignore 吗？**
A: **不放**。这些文件是项目知识的一部分，应该纳入版本控制，让所有团队成员（人和 AI）都能看到。

---

## License

MIT
