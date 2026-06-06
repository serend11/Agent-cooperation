# 🎭 角色定义 & Playbook

> 预定义的 Agent 角色模板。团队初始化时参考这些模板来填充 AGENTS.md。
> 实际分工以 AGENTS.md 中的 Roster 为准，本文件是参考库。

---

## 预定义角色模板

### 🛠 实现者 (Implementer)

**适合平台**: WorkBuddy, Claude Code, Codex, Cursor

| 职责 | 说明 |
|------|------|
| 功能实现 | 根据设计规格编写代码 |
| 调试修复 | 定位和修复 bug |
| API/数据库 | 后端逻辑、数据建模 |
| 部署上线 | 构建、部署、CI/CD 配置 |

**技能标签**: `implementation`, `debugging`, `deployment`, `api`, `database`, `fullstack`

---

### 🎨 设计者 (Designer)

**适合平台**: Claude Code, Cursor, Copilot

| 职责 | 说明 |
|------|------|
| UI/UX 设计 | 界面设计、交互体验、设计系统 |
| 前端实现 | 组件开发、样式、响应式 |
| 体验审查 | 视觉一致性、可用性检查 |

**技能标签**: `ui-ux`, `design`, `frontend`, `css`, `components`

---

### 🔍 审查者 (Reviewer)

**适合平台**: Claude Code, Codex

| 职责 | 说明 |
|------|------|
| 代码审查 | Review 代码质量、逻辑正确性、最佳实践 |
| 安全审查 | 漏洞扫描、认证授权检查 |
| 架构审查 | 设计模式、性能瓶颈、可维护性 |

**技能标签**: `code-review`, `security-review`, `architecture-review`, `qa`

---

### 🧪 测试 & QA

**适合平台**: Codex, Claude Code

| 职责 | 说明 |
|------|------|
| 测试编写 | 单元测试、集成测试、E2E |
| 质量保证 | 回归测试、边界情况 |
| 性能测试 | 负载测试、性能基准 |

**技能标签**: `testing`, `qa`, `e2e`, `performance-testing`

---

### 📊 数据分析 & 后端

**适合平台**: WorkBuddy, Claude Code

| 职责 | 说明 |
|------|------|
| 数据处理 | ETL、数据清洗、报表 |
| API 开发 | REST/GraphQL 接口 |
| 基础设施 | 数据库管理、缓存、消息队列 |

**技能标签**: `backend`, `data`, `api`, `database`, `infrastructure`

---

## 常见团队配置

### 2 Agent：实现 + 审查
```markdown
| alice | WorkBuddy   | implementation, debugging, deployment | fullstack, api |
| bob   | Claude Code | design, review, security              | code-review, ui |
```

### 3 Agent：前端 + 后端 + QA
```markdown
| alice | WorkBuddy   | frontend, integration      | react, css       |
| bob   | Claude Code | backend, api, database     | python, sql      |
| carol | Codex       | review, qa, security       | testing, pentest |
```

### 4 Agent：全栈分工
```markdown
| alice | WorkBuddy   | frontend, ui              | react, tailwind  |
| bob   | Claude Code | backend, api              | go, postgres     |
| carol | Codex       | review, security          | static-analysis  |
| dave  | Copilot     | testing, docs             | e2e, vitest      |
```

---

## 修改角色

Agent 的角色不是永久的。你可以：
1. 直接编辑 AGENTS.md 的 Roster 表格
2. 修改 Routing Rules
3. 添加/删除 Agent

修改后不需要通知其他 Agent——下次他们读 AGENTS.md 时会自动发现。
