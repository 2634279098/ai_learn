# CodeBuddy Agent Team 多智能体协作能力详解

## 一句话总结
CodeBuddy Code v2.95.0 大幅增强了 Agent Team 协作能力，引入队员空闲感知、Plan 审批 UX 对齐、优雅关停握手、队员进度快照等机制，让多 Agent 协作从"能用"进化到"稳定可控"，同时 Bash/PowerShell 工具支持超时自动后台化，彻底解决长命令被误杀的问题。

## 适用场景
- 需要多 Agent 协作完成复杂开发任务（如：一个 Agent 写代码、一个 Agent 做代码审查、一个 Agent 跑测试）
- 频繁运行长耗时命令（`npm install`、`docker build`、`cargo build` 等常超时被杀的构建任务）
- 使用 CodeBuddy/WorkBuddy 进行多任务并行开发的团队
- 希望通过 Plan 模式确保 Agent 执行方向正确的开发者

## 详细内容

### 1. Agent Team 协作四大增强能力

#### (1) 队员空闲感知 — "精确知道队员在做什么"

```yaml
# 新增环境变量
CODEBUDDY_TEAM_IDLE_DETECTION_DISABLED  # 关闭队员空闲感知
```

- Team Lead 能**精确查询**每个队员的实时状态
- 等待指定队员空闲（支持超时和 abort signal）
- 注册"一旦空闲就回调"的钩子
- **实际价值**：UI 展示、任务调度、优雅关停等场景无需再订阅复杂的 stream，一个 API 调用即可获知队员状态

#### (2) Plan 审批 UX 对齐 — "审批体验更直观"

- 队员在 Plan 模式下结束 plan 时，审批弹窗**自动显示**在 lead 侧终端
- 带**队员颜色徽章**区分不同队员
- 三档选项：通过 / 继续规划 / 退出 Plan 模式
- 与常规工具审批的"通过 / 永久通过 / 拒绝"语义明确区分

#### (3) 优雅关停握手 + 超时兜底 — "关停不再是暴力 kill"

```yaml
# 新增环境变量
CODEBUDDY_TEAM_SHUTDOWN_GRACEFUL_TIMEOUT_MS  # 关停兜底超时（默认 15000ms，设为 0 禁用）
```

- `TeamDelete` 或手动关停队员时，lead 会发送**关停请求**等待队员响应
- 若队员在默认 15 秒内无响应，**自动强制终止**进程
- 避免队员卡死导致清理流程永久挂起

#### (4) 队员进度快照 — "实时追踪每个 Agent 的工作"

- 实时追踪每个队员的**工具调用轮数**、**累计生成文本量**
- 记录最近 **10 次工具活动摘要**
- 为 TUI/Web UI 展示"队员正在做什么"打好基础

### 2. Bash/PowerShell 超时自动后台化 — "长命令不再被冤杀"

这是 v2.95.0 最重要的工具层改进：

#### 问题背景
`yarn install`、`docker build`、`cargo build`、`make` 等冷跑命令远超默认 timeout，之前工具层直接 kill 进程。

#### 新机制

| 场景 | v2.94 行为 | v2.95 行为 |
|------|-----------|-----------|
| 命令超时 | 直接 kill 子进程 | **自动转为后台任务继续跑** |
| 用户 Ctrl+C | 杀进程 | 区分"用户主动取消"和"内部流程切换" |
| 内部流程切换 | 冤杀命令 | **转为后台继续跑** |
| 后台任务结果 | 无法获取 | TaskOutput 查询 stdout/stderr/status |

#### 实用配置

```yaml
# 新增环境变量
CODEBUDDY_BASH_ASSISTANT_BUDGET_MS       # 主对话响应预算
CODEBUDDY_BASH_AUTO_BACKGROUND_DISABLED  # 关闭超时自动后台化
```

#### 后台任务状态查询

```bash
# 查看后台任务进度和结果
TaskOutput {task_id}
```

### 3. 使用场景实例

#### 场景一：多 Agent 协作开发

```
主 Agent (Lead)
├── 开发 Agent → 写功能代码
├── 审查 Agent → 代码审查 + 安全审查
└── 测试 Agent → 运行测试套件
```

- Lead 通过空闲感知精确调度队员
- 开发 Agent 进入 Plan 模式时，审批弹窗自动推送到 Lead 终端
- 任务完成后优雅关停队员

#### 场景二：长构建任务不中断

```
# 之前：docker build 到 120 秒被 kill
[ERROR] Command timed out after 120000ms

# 现在：自动转后台继续跑
[INFO] Command automatically backgrounded. Task ID: bg_abc123
> TaskOutput bg_abc123
Build completed successfully in 185s.
```

### 4. v2.95.1 补充改进

- **通用 Agent 支持图像生成与编辑**：子任务中可直接调用 ImageGen/ImageEdit
- **MCP 大响应处理**：超出 token 上限不再直接报错中断，改为保存到会话目录，模型按 offset/limit 分段读取
- **流式响应超时提升**：首 token 等待从 10 分钟提升至 20 分钟
- **Skill 工具描述截断优化**：三阶段均衡截断策略，大量 skills 场景下不再无限膨胀

### 5. 对开发者工作流的启发

1. **复杂任务分解**：将大型开发任务拆分为多个 Agent 协作完成，每个 Agent 专注一个子任务
2. **长任务不中断**：构建、测试、部署等长耗时操作不再担心超时被误杀
3. **Plan 模式审核**：在 Agent 动手前先审查执行计划，避免方向性错误
4. **进度可观测**：通过队员进度快照了解多 Agent 并行工作的实时状态

## 来源
- 原文链接：https://www.codebuddy.cn/docs/cli/release-notes/v2.95.0
- 补充链接：https://www.codebuddy.ai/docs/zh/cli/release-notes/v2.95.1
- 作者/平台：CodeBuddy 官方发布说明

## 标签
#CodeBuddy #Agent #多智能体协作 #自动化 #工作流 #Team
