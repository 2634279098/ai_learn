# Claude Code 完整教学指南

## 一句话总结
从零上手 Anthropic 终端 AI 编程助手，掌握安装配置、核心指令、进阶技巧和 Agent Teams 多代理协作的完整工作流。

## 适用场景
- 想在终端中用自然语言指挥 AI 完成代码编写、Bug 修复、项目重构的开发者
- 需要 Agentic 能力（自主理解需求、规划步骤、执行任务）超越普通代码补全的场景
- 大型项目重构、多模块并行开发等复杂工程任务

## 详细内容

### 一、产品定位与核心优势

Claude Code 是 Anthropic 推出的**终端原生 Agentic Coding Assistant**，由 Claude Opus 4.6 和 Sonnet 4.5 驱动。它不是 IDE 插件式的代码补全工具，而是能**自主理解需求、规划步骤、执行任务、管理多 Agent 协作**的全功能编程助手。

2025 年底已达年化收入 10 亿美元，2026 年已成为最受专业开发者信赖的 AI 代码工具之一。

**五大核心优势**：

| 优势 | 说明 |
|------|------|
| 终端原生 | 无需 IDE，直接在终端使用，支持 macOS / Linux / WSL |
| 强大 Agentic 能力 | 自主执行多步骤任务：读写文件、执行指令、管理 Git |
| Opus 4.6 驱动 | Max 方案使用最强模型，复杂推理超越竞品 |
| Agent Teams | 多实例平行协作，加速大型项目开发 |
| MCP 生态系统 | 通过 Model Context Protocol 整合 GitHub、Slack、数据库等 |

### 二、安装配置（5 步）

#### 步骤一：确认 Node.js 环境（v18+）

```bash
node --version  # 应显示 v18.x 以上
```

macOS 使用 Homebrew：`brew install node`
Ubuntu/Debian：`sudo apt install nodejs npm`

#### 步骤二：全局安装 Claude Code CLI

```bash
npm install -g @anthropic-ai/claude-code
claude --version  # 确认安装成功
```

> 若遇权限问题：加 `sudo` 或使用 nvm 管理 Node 版本

#### 步骤三：进入项目目录

```bash
cd /path/to/your-project
```

Claude Code 会自动读取当前目录所有代码。建议在实际项目文件夹中启动。

#### 步骤四：启动并登录

```bash
claude
```

首次启动输入 `/login`，扫描 QR Code 或点击链接用 Claude.ai 账户登录。
Pro / Max 方案用量直接对应 Claude Code 使用额度。

#### 步骤五：初始化项目记忆

```bash
/init
```

AI 自动分析项目结构，生成 `CLAUDE.md` 文件——这是 Claude Code 的**"项目记忆"**，每次启动自动读取。

### 三、三种启动模式

| 模式 | 命令 | 适用场景 |
|------|------|----------|
| **互动对话模式** | `claude` | 最常用，持续对话迭代，适合复杂任务 |
| **单次执行模式** | `claude "任务描述"` | 一次性任务后退出，适合脚本/CI/CD 整合 |
| **指定模型启动** | `claude --model claude-opus-4-6` | 按需选择模型 |

**单次执行模式实战示例**：

```bash
claude "帮我修复 src/api.js 中的身份验证 bug"
claude "为这个项目建立 README.md，包含安装说明和 API 文档"
claude "重构 components/ 目录，将所有 class component 改为 function component"
```

### 四、核心指令大全

**最重要的 3 个指令**：

| 指令 | 功能 |
|------|------|
| **`/init`** | 初始化项目，生成 CLAUDE.md 记忆文件 |
| **`/memory`** | 查看和编辑 Claude 的记忆内容 |
| **`/mcp`** | 管理 MCP 服务器，连接外部工具 |

**其他实用指令**：

| 指令 | 功能 |
|------|------|
| `/login` | 登录 Anthropic 账户 |
| `/cost` | 查看本次对话的 Token 消耗 |
| `/compact` | 压缩对话记忆，节省 Token |
| `/add-dir` | 扩展 Claude Code 的文件访问范围 |

### 五、进阶技巧

#### 技巧 1：Plan Mode 计划模式

```bash
claude --plan
# 或在对话中说："先帮我规划怎么重构 auth 模块，不要直接动手"
```

- **只规划不执行**，先列出完整任务分解和执行步骤
- 等你确认后才动工
- **强烈推荐用于**：重构大型模块、修改数据库 Schema 等高风险任务

#### 技巧 2：CLAUDE.md 项目记忆最佳实践

```markdown
# 我的 Next.js 项目规范

## 技术栈
- Next.js 15（App Router）
- TypeScript 5.x（严格模式）
- Tailwind CSS v4
- Prisma + PostgreSQL

## 代码风格
- 使用 function component，禁止 class component
- 每个组件都要有 TypeScript 类型定义
- 使用 const 箭头函数，不使用 function 关键字

## 测试规范
- 单元测试使用 Vitest
- E2E 测试使用 Playwright
- 新功能必须附上测试

## 禁止事项
- 不得直接修改 package.json 的 dependencies
- 不得删除任何现有测试
```

> 效果：Claude Code 每次保持一致代码风格，大幅减少重复说明

#### 技巧 3：MCP 整合扩展能力

通过 `/mcp` 指令可整合的外部能力：

| MCP 类型 | 能力 |
|----------|------|
| **GitHub MCP** | 读取 PR/Issue，自动提交、建立 Branch |
| **数据库 MCP** | 连接 PostgreSQL/MySQL，直接查询和修改数据 |
| **Slack MCP** | 读取频道消息，自动发送通知 |
| **Filesystem MCP** | 访问项目目录外的文件（突破沙盒限制） |
| **自定义 MCP** | 整合任何 REST API 或内部工具 |

#### 技巧 4：Agent Teams 多代理协作（Max 方案专属）

- 一个**主 Agent** 负责规划和协调
- 多个**子 Agent** 平行执行不同模块开发
- 适合场景：大型重构、多模块同步开发、测试套件生成
- 搭配**后台执行**（按 Esc × 2 不等待结果）大幅提升效率

### 六、定价方案详解

Claude Code 与 Claude.ai 账户方案直接挂钩：

| 方案 | 价格 | 核心权益 |
|------|------|----------|
| **Free** | $0 | 每日限量使用，适合轻度体验 |
| **Pro** | $20/月 | 完整 Claude Code 用量，**推荐个人开发者** |
| **Max** | $100/月 | 最高用量上限、Opus 4.6 优先访问、Agent Teams |
| **API** | 按量计费 | Input $3–$15 / 1M tokens，Output $15–$75 / 1M tokens |

### 七、与其他主流工具对比

| 工具 | 最适合场景 |
|------|-----------|
| **Claude Code** | 命令行工作流、强大 Agentic 能力、复杂多步骤自动化 |
| **Cursor** | IDE 视觉界面、多 AI 模型切换、全栈开发主力 |
| **GitHub Copilot** | 企业环境、SOC 2 合规、深度整合 GitHub 生态 |
| **Windsurf** | 预算有限、高性价比、Cascade Agent 应付日常开发 |

**专业开发者最佳实践**：Claude Code Max（复杂 Agent 任务）+ Cursor Pro（日常 IDE 开发）并用

### 八、常见问题

**Q：支持 Windows 吗？**
> 原生支持 macOS 和 Linux。Windows 需通过 **WSL 2**（安装 Ubuntu 后再 npm install）

**Q：Token 用量会不会很烧钱？**
> 使用 `/cost` 随时查看消耗；善用 `/compact` 压缩对话记忆可有效节省 Token

**Q：能访问整个电脑吗？**
> 默认**只能访问启动时所在目录**。可用 `/add-dir` 扩展范围。所有文件操作**默认有确认提示**

## 来源
- 原文链接：https://weavai.app/blog/zh-cn/2026/05/26/2026-claude-code-%e5%ae%8c%e6%95%b4%e6%95%99%e5%ad%a6%ef%bc%9a%e4%bb%8e%e5%ae%89%e8%a3%85%e5%88%b0%e8%bf%9b%e9%98%b6%e6%8a%80%e5%b7%a7/
- 作者/平台：AI 织梦博客（weavai.app）

## 标签
#ClaudeCode #终端编程 #AI编程 #Agent #最佳实践 #Anthropic
