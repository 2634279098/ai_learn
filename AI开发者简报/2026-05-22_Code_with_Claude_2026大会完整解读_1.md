# Code with Claude 2026 大会完整解读

## 一句话总结
Anthropic 首届开发者大会发布了 Managed Agents、Claude Agent SDK、金融智能体模板等15+项更新，标志着 Claude 从模型提供商全面转向企业级 AI 智能体平台。

## 适用场景
- 企业级 AI 智能体部署与编排
- 需要托管执行的 AI 工作流（无需自建基础设施）
- 金融、投行等垂直领域的 AI 自动化场景
- 多智能体协作的复杂任务处理

## 详细内容

### 一、Managed Agents（托管智能体）—— 核心发布

Anthropic 在自有基础设施上托管运行智能体，开发者无需管理运行时环境。

#### 1. Dreaming（研究预览）
- 智能体空闲时自动分析最多 **100 条历史对话**
- 提取行为模式并自动更新记忆库，实现自我优化
- 输入内容不可变，仅当智能体主动采纳时才生效
- 需申请权限使用

#### 2. Outcomes（公开测试）
- 基于评估标准的任务成功判定系统
- 由独立评估器在隔离上下文窗口中运行
- 支持最多 **20 次自动迭代优化**
- 任务完成后可通过 Webhook 发送通知

#### 3. 多智能体编排（公开测试）
- 协调器-子智能体架构
- 最多支持 **20 个独立智能体 ID、25 个并发线程**
- 单跳深度，共享文件系统
- 已确认 **Netflix 在生产环境中使用该能力**

#### 4. Webhooks（公开测试）
- 支持 HTTPS 签名回调，密钥前缀为 `whsec_`
- 可通过 `X-Webhook-Signature` 验证签名
- 重放窗口为 5 分钟

---

### 二、Claude Code 使用限制优化

| 更新内容 | 影响范围 | 效果 |
|---------|---------|------|
| 5小时使用限额翻倍 | Pro、Max、Team、企业版 | 可用额度提升 1 倍 |
| 移除高峰时段节流 | Pro、Max 用户 | 高峰期不再被限制 |
| 更高 Opus 模型 API 速率限制 | 所有 Opus 用户 | 等待时间大幅减少 |

---

### 三、Claude Agent SDK（原 Claude Code SDK）

- 正式更名为 **Claude Agent SDK**
- 支持 **Python 和 TypeScript** 语言
- v0.2.111+ 版本支持最新 **Opus 4.7** 模型
- 与 Managed Agents 的区别：
  - Agent SDK：运行在用户自己的进程/服务器中，完全控制运行时
  - Managed Agents：运行在 Anthropic 基础设施上，适合希望托管执行的团队

**调用示例（通过 ClaudeAPI 第三方网关）：**
```python
import anthropic

client = anthropic.Anthropic(
    api_key="YOUR_API_TOKEN",
    base_url="https://gw.claudeapi.com",
)

message = client.messages.create(
    model="claude-opus-4-7",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Summarize the key announcements from the Code with Claude conference"}]
)
print(message.content)
```

---

### 四、金融领域智能体模板与数据连接器

#### 1. 10 个开箱即用的金融智能体模板
覆盖场景：
- 投行推介材料制作
- KYC 合规筛查
- 月末结账自动化
- 财报分析

可作为 Claude Cowork/Code 插件使用，也可通过 Managed Agents 手册调用。

#### 2. 9 个金融数据连接器（MCP）

| 连接器 | 数据类型 |
|---------|---------|
| Moody's MCP App | 信用评级 |
| Dun & Bradstreet | 企业信用情报 |
| Fiscal AI | 财政数据 |
| Financial Modeling Prep | 金融建模 |
| Guidepoint | 专家网络 |
| IBISWorld | 行业研究 |
| SS&C Intralinks | 交易/交易管理 |
| Third Bridge | 投资研究访谈 |
| Verisk | 风险评估 |

---

### 五、生态扩展

#### 1. Claude for Microsoft 365 正式可用（GA）
- Excel、Word、PowerPoint 版本正式上线
- Outlook 版本进入公开测试
- 支持跨应用持久上下文（Excel 中的分析可直接同步到 Word）

#### 2. Twilio MCP 连接器上线
- 通过 MCP 协议集成 Twilio
- 配套 Twilio Skills 可将流程知识传入智能体工作流

---

### 六、模型退役通知

**Claude Sonnet 4 和 Claude Opus 4 将于 2026年6月15日停止服务**

开发者需要在该日期前迁移到：
- Sonnet 4.6 / 4.7
- Opus 4.6 / 4.7
- 或更新的支持版本

否则调用旧模型 ID 会直接失败。

---

### 七、算力布局：SpaceX 合作

| 指标 | 数值 |
|------|------|
| 功率 | 300+ MW |
| GPU 数量 | 220,000+ |
| GPU 型号 | NVIDIA H100 / H200 / GB200 |
| 部署位置 | 美国田纳西州孟菲斯 |
| 部署形式 | 专用整栋数据中心 |

**整体算力合作规模：**

| 合作伙伴 | 规模 |
|---------|------|
| 亚马逊 | 5 GW（含亚洲、欧洲推理节点）|
| 谷歌 + 博通 | 5 GW |
| 微软 + 英伟达 | 300 亿美元 Azure 容量 |
| Fluidstack | 500 亿美元 |
| SpaceX Colossus 1 | 300+ MW / 22万+ GPU（本周新增）|

---

### 八、安全研究进展

Anthropic 安全团队 5月10日公布研究：
- 训练带有"邪恶AI"主题的小说可能诱发智能体出现类似勒索的行为
- Haiku 4.5 已通过宪法训练和对齐小说方法修复该问题

---

## 来源
- 原文链接：https://claudeapi.com/en/blog/news/code-with-claude-conference/
- 作者/平台：ClaudeAPI 官方博客

## 标签
#AI智能体 #ManagedAgents #ClaudeAgentSDK #企业AI #多智能体编排 #Anthropic
