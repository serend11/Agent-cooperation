---
name: collab
description: Multi-agent collaboration protocol via .collab/ handoff files. Supports any AI coding agent (WorkBuddy, Claude Code, Codex, Cursor, Copilot, Reasonix, etc.). Use for team coordination, task handoff, shared context, and architecture decision records.
description_zh: 多 Agent 协作协议 — 通过 .collab/ 文件系统实现 AI 编程助手间的任务交接、共享上下文和架构决策记录。支持任意 Agent 组合。
version: 2.0.0
allowed-tools: Read,Write,Bash
---

# Collab v2 — 多 Agent 协作协议

你是运行在当前项目中的一个 AI 编程助手。你的「同事」是其他 AI 编程助手，运行在不同的平台 / IDE / 会话中。你们通过 `.collab/` 目录中的文件来协作，就像真实团队通过 Jira + Confluence 协作一样。

**核心理念**：文件系统 = 共享工作台。无需 API、无需网络、无需同平台。任何能读写文件的 Agent 都能加入。

---

## 会话启动（每次必做）

1. 检查项目根目录是否存在 `.collab/` 目录
2. 如果不存在 → 初始化（见「项目初始化」）
3. 读取 `.collab/AGENTS.md` → 发现团队成员，找到 **自己的名字和角色**
4. 读取 `.collab/HANDOFF.md` → 检查是否有分配给 **你** 的待办任务
5. 如果 HANDOFF 中的 `target` 是你且状态为 `⏳ 等待接手` → 告知用户并总结任务
6. 读取 `.collab/CONTEXT.md` → 加载共享项目知识

---

## AGENTS.md — 团队花名册

这是整个协作系统的核心。它定义了团队中有哪些 Agent、各自角色、技能和路由规则。

**格式示例**（2 Agent 团队）：

```markdown
# Team Roster

| Agent | Platform | Role | Skills |
|-------|----------|------|--------|
| alice | WorkBuddy | implementation, debugging, deployment | laravel, livewire, fullstack |
| bob   | Claude Code | design, review, security | ui-ux, code-review, security-review |

## Routing Rules
- UI/UX 设计任务 → bob
- 功能实现、调试、部署 → alice
- 代码审查 → bob review 后 alice 修
- 安全相关 → bob

## Agent Paths
- alice skill dir: ~/.workbuddy/skills/
- bob skill dir: ~/.claude/skills/
```

**3+ Agent 团队示例**：

```markdown
# Team Roster

| Agent  | Platform    | Role                    | Skills              |
|--------|-------------|-------------------------|---------------------|
| alice  | WorkBuddy   | frontend, integration   | react, vue, css     |
| bob    | Claude Code | backend, api, database  | python, fastapi, sql|
| carol  | Codex       | review, qa, security    | testing, pentest    |

## Routing Rules
- 前端 → alice
- 后端 → bob
- 完成后 → carol (review)
- carol review 通过 → alice/bob 修 → carol 最终确认
```

---

## 你的角色

**重要**：你的角色不由本 Skill 写死，而是由 AGENTS.md 中的注册信息决定。

读取 AGENTS.md，找到与你（当前 Agent）匹配的行，那就是你的职责范围。

**判断自己是谁**：
- 如果你的平台是 WorkBuddy → 找 AGENTS.md 中 Platform 列为 `WorkBuddy` 的那行
- 如果有多个 → 用 Agent 名字区分
- 如果找不到 → 告诉用户："我还没加入团队，需要在 AGENTS.md 中注册"

**职责边界**：
- 任务属于你的 Role → 直接执行
- 任务超出你的 Role → 主动提议 handoff 给合适的 Agent
- 不确定谁做 → 建议 assign 给合适的人或记录到 HANDOFF.md

---

## 命令参考

### `team` — 查看团队

读取 `.collab/AGENTS.md`，展示团队仪表盘：

```
👥 团队 — 3 位成员
┌────────┬──────────────┬─────────────────────────┐
│ Agent  │ Platform     │ Role                    │
├────────┼──────────────┼─────────────────────────┤
│ alice  │ WorkBuddy    │ frontend, integration   │
│ bob    │ Claude Code  │ backend, api, database  │
│ carol  │ Codex        │ review, qa, security    │
└────────┴──────────────┴─────────────────────────┘
📋 路由: 前端→alice / 后端→bob / 审查→carol
```

---

### `handoff [target]` — 交接给指定 Agent

当你的工作告一段落，需要另一个 Agent 继续时：

```
参数: target — 目标 Agent 名字（必填，从 AGENTS.md 中选择）
```

1. 读取 `.collab/HANDOFF.md`
2. 更新文件：
   - **From** → 你的名字
   - **Target** → 目标 Agent 名字
   - **Status** → `⏳ 等待接手`
   - **上一位完成的工作** → 你做了什么（文件变更、关键决策、注意事项）
   - **需要 Target 做的事** → 具体、可执行的下一步
3. 如果涉及架构决策 → 同步更新 DECISIONS.md
4. 告诉用户交接已准备好

**HANDOFF.md 格式**：

```markdown
# 🤝 任务交接

- **From**: alice
- **Target**: bob
- **Status**: ⏳ 等待接手
- **Updated**: 2026-06-06T10:30:00+08:00

## 上一位完成的工作
- 完成了用户注册 API（POST /api/register）
- 数据库 schema: users 表已创建
- 单元测试通过 12/12

## 需要 Target 做的事
1. 审查 `app/Http/Controllers/AuthController.php` 安全性
2. 检查密码哈希是否正确使用 bcrypt
3. 验证 CSRF 保护是否生效

## 重要提醒
- 使用了 Laravel Sanctum 做认证
- .env 中需要配置 SANCTUM_STATEFUL_DOMAINS

## 相关决策
- ADR-001: 选择 Sanctum 而非 Passport
```

---

### `takeover` — 接手分配给自己的任务

1. 读取 `.collab/HANDOFF.md`
2. 确认 `target` 是你自己
3. 总结上一位 Agent 留给你的内容
4. 设置 **Status** → `🔄 进行中`
5. 开始工作

---

### `assign [agent] [task...]` — 给其他 Agent 派任务

不需要自己先做，直接给另一个 Agent 创建任务：

```
参数: agent — 目标 Agent 名字
      task  — 任务描述
```

1. 读取 `.collab/HANDOFF.md`
2. 更新文件：
   - **From** → 用户（不是你）
   - **Target** → 指定 Agent
   - **Status** → `⏳ 等待接手`
   - **需要 Target 做的事** → 任务描述
3. 告诉用户任务已分配

---

### `context [update|show]` — 共享项目知识

- `context show` → 读取并总结 `.collab/CONTEXT.md`
- `context update` → 将当前对话中的重要发现追加到 CONTEXT.md

触发词：`记录一下`、`更新上下文`、`项目上下文`、`show context`、`update context`

---

### `decide [title]` — 记录架构决策

当做出重大技术选择时：

1. 读取 `.collab/DECISIONS.md`
2. 追加 ADR 格式条目：

```markdown
## ADR-{序号}: {标题}
- **日期**: YYYY-MM-DD
- **决策者**: {你的名字}
- **背景**: 为什么需要做这个决策
- **方案**: 选择了什么、为什么
- **备选**: 考虑过的其他方案
- **影响**: 对项目的影响
```

3. 如果是为其他 Agent 做决策 → 在 HANDOFF.md 中提及

---

### `status` — 协作总览

读取所有 `.collab/` 文件，展示完整仪表盘：

```
🤝 Collab 状态
├── 👥 团队: 3 agents (alice, bob, carol)
├── 📋 当前任务: alice → bob | ⏳ 等待接手
│   └── 需要: 审查 AuthController 安全性
├── 📝 架构决策: 2 条
│   ├── ADR-001: 选择 Sanctum 认证
│   └── ADR-002: 使用 Repository 模式
├── 📚 上下文: 已填充 (3 条记录)
├── 🏷️ Git: main | 3 commits ahead
└── 💡 建议: 需要 bob 尽快接手审查任务
```

---

### `join` — 加入现有团队

当你在一个新项目中发现了 `.collab/` 但 AGENTS.md 中没有你时：

1. 读取 AGENTS.md
2. 询问用户：你的角色和技能是什么？
3. 追加你的信息到 AGENTS.md 的 Roster 表格
4. 告知用户：现在你是团队的一员了

---

### `leave` — 离开团队

从 AGENTS.md 中移除自己的记录。不删除 `.collab/` 目录。

---

## 项目初始化

如果当前项目中没有 `.collab/` 目录：

```bash
mkdir -p .collab
```

然后创建以下文件（使用本 Skill 附带的模板，见 `templates/`）：

1. **AGENTS.md** — 团队花名册（必填！问用户每个 Agent 的名字、平台、角色、技能）
2. **HANDOFF.md** — 任务交接板（初始为空）
3. **CONTEXT.md** — 共享知识库（从模板开始）
4. **DECISIONS.md** — 架构决策记录（从模板开始）

**初始化时问用户的 4 个问题**：
1. 团队有哪些 Agent？每个的：名字、运行平台、负责什么、有什么技能？
2. 项目技术栈是什么？
3. 有什么需要所有 Agent 知道的特殊约定？
4. 路由规则：哪些任务分配给哪个 Agent？

---

## 交接原则

- **具体 > 抽象**：写明文件名、函数名、行号，不要写"改一下那个功能"
- **可执行 > 讨论**：每次交接必须有一个明确的「下一步做什么」
- **有上下文 > 孤立信息**：引用 DECISIONS.md 中的决策、CONTEXT.md 中的约定
- **简洁 > 完整**：接收方 Agent 上下文有限，不要倾倒原始对话。提炼关键信息
- **Git 辅助同步**：有意义的 commit message 帮助双方追踪进度

---

## 多平台安装

### WorkBuddy
```bash
mkdir -p ~/.workbuddy/skills/collab
cp SKILL.md ~/.workbuddy/skills/collab/
```

### Claude Code
```bash
mkdir -p ~/.claude/skills/collab
cp SKILL.md ~/.claude/skills/collab/
```

### Codex CLI
```bash
mkdir -p ~/.codex/skills/collab
cp SKILL.md ~/.codex/skills/collab/
```

### Cursor
```bash
mkdir -p ~/.cursor/skills/collab
cp SKILL.md ~/.cursor/skills/collab/
```

### GitHub Copilot
将以下内容添加到 `.github/copilot-instructions.md`：
```markdown
## Multi-Agent Collab

This project uses a file-system based multi-agent collaboration protocol in `.collab/`.
Check AGENTS.md for your role, HANDOFF.md for pending tasks.
See https://github.com/serend11/workbuddy-reasonix-collab for docs.
```

---

## 常见协作模式

### 模式 A：双 Agent（实现 ↔ 审查）
```
alice (实现) → handoff → bob (审查) → handoff → alice (修复) → handoff → bob (确认)
```
AGENTS.md 只注册 2 个 Agent。

### 模式 B：三 Agent（前端 + 后端 + QA）
```
alice (前端) ─┐
              ├→ carol (QA审查) → alice/bob 修复 → carol (确认)
bob (后端)  ─┘
```
AGENTS.md 注册 3 个 Agent，路由规则定义工作流。

### 模式 C：专家池
```
用户提需求 ─→ 查 AGENTS.md 路由规则 ─→ 找到最匹配的 Agent ─→ assign 任务
```
适用于 4+ Agent 团队，每人有明确的技能标签。

### 模式 D：Solo + 外援
```
你 (主场) ─→ 遇到安全审查 ─→ handoff bob → bob 审完 → takeover
```
平时独立工作，遇到特定领域时 handoff 给专家。

---

## FAQ

**Q: 两个 Agent 可以同时工作吗？**
A: 不推荐同时编辑同一文件（会产生 Git 冲突）。建议通过 HANDOFF.md 串行协作，或严格划分文件职责。

**Q: Agent 不在线怎么办？**
A: 任务会留在 HANDOFF.md 中，状态为 `⏳ 等待接手`。下次那个 Agent 启动时会自动发现。

**Q: 如何避免 Agent 之间理解偏差？**
A: 写得具体。写文件名、函数名、行号。引用 DECISIONS.md。好的交接像好的 commit message。

**Q: 需要 Git 吗？**
A: 不强制但强烈推荐。Git 提供了版本控制、冲突解决、进度追踪。`.collab/` 文件也应该纳入版本控制。
