# Claude Code Agent SDK 新额度政策（6月15日生效）

## 一句话总结
Anthropic 将 Agent SDK 用量从订阅主额度中剥离，改为独立月度配额制，Pro 用户每月仅获 $20 额度，重度 Agent 用户可用量缩水近十倍。

## 适用场景
- 使用 Claude Code `claude -p` 非交互模式进行自动化任务的开发者
- 基于 Claude Agent SDK（Python/TypeScript）构建自动化工作流的用户
- 通过 GitHub Actions 使用 Claude Code 的 CI/CD 流水线
- 使用 OpenClaw 等第三方 Agent 应用的用户

## 详细内容

### 核心变更

| 维度 | 变更前（6月15日前） | 变更后（6月15日起） |
|------|-------------------|-------------------|
| Agent SDK / `claude -p` | 计入订阅主额度（自助餐模式） | 独立月度额度（定量配给模式） |
| 交互式 Claude Code | 使用订阅限制 | **不变**，继续使用订阅限制 |
| Claude Cowork / 网页对话 | 使用订阅限制 | **不变**，继续使用订阅限制 |
| API 密钥用户 | 按量付费 | **不变**，不受此政策影响 |

### 各计划月度额度

| 计划类型 | 月度额度（美元） |
|----------|------------------|
| **Pro** | **$20** |
| **Max (5x)** | **$100** |
| **Max (20x)** | **$200** |
| **Team 标准席位** | **$20** |
| **Team 高级席位** | **$100** |
| **Enterprise（基于使用量）** | **$20** |
| **Enterprise 高级席位** | **$200** |

### 额度涵盖范围

**适用于**：
- ✅ Python 或 TypeScript 的 Claude Agent SDK 调用
- ✅ `claude -p` 非交互模式命令
- ✅ Claude Code GitHub Actions 集成
- ✅ 通过 Agent SDK 使用 Claude 订阅身份验证的第三方应用（如 OpenClaw）

**不适用于**：
- ❌ 终端或 IDE 中的交互式 Claude Code
- ❌ Web / 桌面 / 移动端的 Claude 对话
- ❌ Claude Cowork 协作模式

### 6 条关键规则

1. **按用户分配，不可共享** — 额度属于个人账户，不能在队友之间共享或汇总
2. **每月刷新，不累积** — 未使用的额度不会结转到下个计费周期
3. **一次性选择加入** — 通过 Claude 账户申领一次，之后自动刷新
4. **优先扣除** — Agent SDK 使用从月度额度中优先扣除
5. **超额处理** — 额度用尽后：启用额外使用→按标准 API 费率计费；未启用→Agent SDK 请求停止
6. **Enterprise 特别说明** — 基于席位的 Enterprise 计划中，标准席位成员不获得 Agent SDK 月度额度

### 对重度用户的影响

Anthropic 将 Agent SDK 用量从"自助餐"改成了"定量配给"。以 Pro 用户为例：
- **旧模式**：Agent SDK 用量共享 Pro 订阅的主额度（如每月 $20 总量）
- **新模式**：交互式使用保留订阅额度 + Agent SDK 独立 $20 额度
- 实际上对轻度用户是利好（独立额度不占主额度），但对重度 Agent 自动化用户（如大量使用 `claude -p` 或 GitHub Actions），可用量可能缩水近十倍

### 开发者应对建议

1. **评估当前 Agent SDK 用量** — 使用 `/cost` 或 `ccusage` 工具查看历史消耗
2. **区分使用场景** — 个人实验和轻量自动化用订阅额度，生产级自动化建议切换到 Claude Platform + API 密钥（按量付费更可预测）
3. **优化 Token 消耗** — 通过 CLAUDE.md 分层、Prompt Cache 命中、`--bare` 模式等手段减少每次调用的 Token 消耗
4. **Team 管理员注意** — 合格用户将在 6 月 15 日前收到领取说明邮件，无需管理员操作
5. **关注 OpenAI 动态** — 同一周 OpenAI 向企业用户推出 Codex 两个月免费迁移，竞争格局值得关注

### 同期行业动态

在同一周内：
- **OpenAI** 向企业用户推出 Codex 两个月免费迁移，吸引 Claude Code 用户
- **Microsoft** 大规模取消 Claude Code 授权，内部强制向 Copilot CLI 迁移
- **xAI Grok Build** 正式入场早期测试

这标志着 AI 编程工具领域的定价竞争全面升级。

## 来源
- 原文链接：https://support.claude.com/zh-CN/articles/15036540
- 作者/平台：Anthropic 官方支持文档
- 参考分析：https://www.51cto.com/article/843326.html（51CTO 解读）
- 参考分析：https://gipyeong-lee.github.io/2026/05/14/Use-the-Claude-Agent-SDK-with-Your-Claude-Plan.zh-cn/（技术博客解读）

## 标签
#效率 #工作流 #AI编程 #Claude #成本优化 #Agent
