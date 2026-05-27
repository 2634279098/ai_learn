# Claude Code Token 成本优化实战：查砍保控四步法

## 一句话总结
通过"查→砍→保→控"四步框架，系统化诊断和优化 Claude Code 的 Token 消耗，实测单会话输入 Token 从 13,325 降至 3,225（降幅 76%），结合 Batch + Cache 叠加可实现输入成本 95% 折扣。

## 适用场景
- Claude Code 使用频率高、Token 消耗快的开发者
- 订阅额度经常不够用的 Pro/Max 用户
- 在 CI/CD 中使用 `claude -p` 进行自动化的场景
- 使用多 Agent 协作（Teammate/Worktree）导致 Token 放大的团队

## 详细内容

### 第一步：查 — Token 消耗诊断

#### 1. `/cost` 会话级成本快照

在对话中输入 `/cost` 查看当前会话用量，重点观察：

| 指标 | 含义 | 健康信号 |
|------|------|---------|
| 输入 Token | 每轮发送给模型的上下文总量 | 越低越好 |
| 输出 Token | 模型生成的回复量 | 稳定即可 |
| **缓存命中 Token** | 被 Prompt Cache 覆盖的输入部分 | **占比越高越健康** |

> 缓存命中部分按 **0.1x 计费（90% 折扣）**，是成本优化的核心杠杆。

#### 2. `ccusage` 历史趋势 CLI 分析

```bash
npx ccusage@latest daily    # 按天统计
npx ccusage@latest monthly  # 月度汇总
npx ccusage@latest session  # 按会话分组
```

显示缓存创建和缓存读取的 token 数，支持 `--offline` 离线模式和 JSON 导出。
来源：[GitHub - ryoppippi/ccusage](https://github.com/ryoppippi/ccusage)

#### 3. `cc-lens` 可视化监控 Dashboard

```bash
npx cc-lens
```

自动打开浏览器展示 Token 时间线、模型占比、缓存效率面板，数据每 5 秒刷新，适合当实时监控屏。
来源：[GitHub - Arindam200/cc-lens](https://github.com/Arindam200/cc-lens)

#### 4. `/context` 上下文空间检查

检查未使用的 MCP 服务器是否白白占用上下文空间，以及文件读取、搜索、MCP 返回数据的体积。

#### 5. 输入 Token 三大来源

| 来源 | 说明 | 优化方向 |
|------|------|---------|
| **持久规则** | CLAUDE.md + rules/ 自动加载文件，每轮必注入 | 砍"月租" |
| **对话历史** | 上下文窗口中累积的消息 | 控制会话长度 |
| **工具结果** | 文件读取、搜索、MCP 返回数据 | 排除噪声、精简 MCP |

---

### 第二步：砍 — 削减固定输入成本

#### 核心案例：CLAUDE.md 三层架构

| 指标 | 优化前 | 优化后 | 节省 |
|------|--------|--------|------|
| 每会话固定 Token | **13,325** | **3,225** | **10,100（降幅 76%）** |

**第一层：顶层 CLAUDE.md（每次必加载，~765 tokens）**

```markdown
# Claude 全局指令

## 语言与风格
- 全局默认中文
- 使用简洁文言风格节省 token

## 全局规则（每次自动加载）
- 通用经验 → rules/lessons-learned.md
- Skill 开发偏好 → rules/skill-development.md

## 按需参考（涉及相关任务时用 Read 读取）
- 服务器信息 → ~/.claude/refs/servers.md
- 数据库连接 → ~/.claude/refs/database-connections.md
```

**第二层：rules/ 目录（规则系统自动加载，~2,460 tokens）**

| 文件 | 估算 Tokens | 加载方式 |
|------|------------|---------|
| `lessons-learned.md` | ~1,600 | 每次自动加载 |
| `skill-development.md` | ~140 | 每次自动加载 |
| `discernment.md` | ~720 | 每次自动加载 |

**第三层：refs/ 目录（纯按需加载，零固定消耗）**

| 文件 | 估算 Tokens | 加载方式 |
|------|------------|---------|
| `servers.md` | ~2,400 | 涉及服务器时 Read |
| `database-connections.md` | ~2,100 | 涉及数据库时 Read |

> **关键操作**：把不是每次都用的内容从 CLAUDE.md 移到 `refs/` 目录，在 CLAUDE.md 里写一行"涉及 XX 时用 Read 读取 refs/XX.md"。

#### 写作风格优化

```markdown
# 冗长写法（~45 tokens）
When you are writing code, please make sure to use ES modules syntax (import/export)
instead of CommonJS (require). This is important for consistency across the codebase.

# 精简写法（~15 tokens）
- 用 ES modules，不用 CommonJS
```

#### 排除噪声文件

| 方式 | 配置 | 效果 |
|------|------|------|
| Glob 尊重 `.gitignore` | `CLAUDE_CODE_GLOB_NO_IGNORE=false` | 排除 `.gitignore` 路径 |
| `permissions.deny` | `.claude/settings.json` 中配置 deny 规则 | 硬性禁止读取特定路径 |
| 禁用未用 MCP 服务器 | `/context` 检查后手动禁用 | 消除无用 MCP 上下文占用 |

---

### 第三步：保 — Prompt Cache 最大化命中

#### 缓存机制原理

| 条件 | 说明 |
|------|------|
| **前缀 100% 一致** | 系统提示、工具定义、对话历史前缀必须完全匹配 |
| **最小 Token 门槛** | Opus 4.6/Haiku 4.5: 4,096; Sonnet 4.6: 2,048 |
| **TTL** | 默认 **5 分钟**（每次命中自动刷新）；API 可延长到 1 小时 |
| **失效原因** | 切换模型/effort、修改工具或 MCP 配置、超 TTL、前缀变化 |

#### 缓存定价

| 类型 | 倍率（相对 base input price） |
|------|-----|
| 5 分钟缓存写入 | **1.25x** |
| 1 小时缓存写入 | 2x |
| **缓存命中/读取** | **0.1x（90% 折扣）** |

> **5 分钟缓存写入花 1.25x，一次命中即回本。**

#### 版本升级修复的关键缓存 Bug

| 版本 | 修复内容 | 影响 |
|------|---------|------|
| **v2.1.72** | 修复 SDK `query()` 调用缓存失效 bug | 修复前该场景输入成本最高为修复后 **12 倍** |
| **v2.1.86** | 移除工具描述中的动态内容 | 提升 Bedrock/Vertex/Foundry 缓存命中率 |
| **v2.1.85** | 启用 ToolSearch 时全局系统提示缓存生效 | 含 MCP 用户受益 |
| **v2.1.90** | 修复 `--resume` 首次请求 deferred tools 导致缓存未命中 | 恢复会话成本降低 |

> 确保版本 `claude --version` ≥ **v2.1.72**。

---

### 第四步：控 — 预算护栏与使用习惯

#### 预算护栏（`-p` / print 模式）

```bash
# 单会话美元上限
claude -p --max-budget-usd 5.00 "your query"

# 限制最大交互轮次
claude -p --max-turns 3 "your query"

# 最小启动模式：跳过所有非必要加载项
claude --bare -p "your query"
```

`--bare` 模式跳过：hooks、LSP、插件同步、Skill 扫描、自动记忆、CLAUDE.md、MCP 服务器发现。启动速度比标准模式快约 **14%**，适合 CI/CD 脚本化调用。

#### 思考深度控制

```json
// .claude/settings.json
{
  "effortLevel": "medium"
}
```

```bash
# 命令行临时指定
claude --effort low "简单任务"
```

`effortLevel` 仅对支持 extended thinking 的模型生效（如 Opus 4.6）。日常任务用 `medium`，复杂架构设计切 `high`。

#### 日常使用习惯

| 习惯 | 操作 | 原因 |
|------|------|------|
| **编辑原始消息而非追问** | 回到原始消息编辑后重新生成 | 新消息会重新发送完整对话历史 |
| **及时 `/compact`** | 上下文使用约 **60%** 时执行 | 此时摘要质量最高 |
| **换任务用 `/clear`** | 探索性和定向会话分开 | 避免历史噪声干扰 |
| **检查 MCP 噪声** | 用 `/context` 检查并禁用 | 未使用的 MCP 白白占上下文 |

#### Model-Mixing 策略

| 模型 | Input ($/MTok) | Output ($/MTok) | 适用场景 |
|------|---------|----------|---------|
| Haiku 4.5 | **$1.00** | **$5.00** | 分类、摘要、格式化 |
| Sonnet 4.6 | $3.00 | $15.00 | 日常开发任务 |
| Opus 4.6 | $5.00 | $25.00 | 复杂推理和 agentic loop |

**成本对比**（1M input + 100K output）：
- 全用 Opus 4.6：$7.50
- 全用 Haiku 4.5：**$1.50**
- Opus + 缓存命中：$3.00
- **Batch + Cache 叠加极限**：输入成本 **$0.25/MTok**（95% 折扣）

---

### 优化手段总览

| 优化手段 | 节省效果 | 难度 |
|---------|---------|------|
| CLAUDE.md 分层按需 | 固定输入减少 **76%** | 低 |
| Prompt Cache 命中 | 命中部分享 **90% 折扣** | 低 |
| SDK cache bug 修复 | 修复前最高 **12x** 浪费 | 低 |
| 编辑原始消息 | 显著减少重复 token | 低 |
| `--bare` 模式 | 启动快 **14%** | 低 |
| effortLevel 降级 | 减少思考 token | 低 |
| Model-Mixing | 单价比 **1:3:5** | 高 |
| Batch + Cache 叠加 | 输入成本 **95% 折扣** | 高 |

### 5 分钟成本体检清单

```
□ 跑一次 /cost，记录当前输入/输出/缓存命中比例
□ 升级到最新版本（claude --version 确认 ≥ v2.1.72）
□ CLAUDE.md 超过 1000 tokens？拆分到 rules/ 和 refs/
□ 确认 .gitignore 已排除 node_modules 等噪声目录
□ 安装 ccusage 开始追踪历史消耗趋势
```

## 来源
- 原文链接：https://blog.deepai.wiki/posts/claude-code-cost-optimization/
- 作者/平台：DeepAI Wiki 技术博客
- 官方参考：https://www.anthropic.com/pricing（Anthropic 官方定价页）
- 工具来源：[ccusage](https://github.com/ryoppippi/ccusage)、[cc-lens](https://github.com/Arindam200/cc-lens)

## 标签
#效率 #工作流 #AI编程 #Claude #成本优化 #Token #PromptCache
