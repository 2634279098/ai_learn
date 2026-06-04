# Cursor 3 Agents Window 多智能体协作

## 一句话总结
Cursor 3 从"智能文件编辑器"升级为"Agent 调度中心"，核心是左侧 Agents Window 面板统一管理多个并行 Agent，让开发者从"提问者"变为"调度者"。

## 适用场景
- 大型重构需要多模块并行修改
- 前端开发需要实时预览并可视化标注 UI 修改
- 团队需要跨仓库协作（前端+后端+共享库）
- 需要在多个模型间对比选型
- 需要 AI 自动化处理 Jira 任务和 PR

## 详细内容

### 核心新功能详解

#### 1. /multitask：并行执行多个 Agent
将一个复杂任务自动拆解为多个独立子任务，同时启动多个异步子 Agent 并行执行。

```bash
/multitask 添加用户认证模块，并同步写单元测试
```

- 实测速度提升显著，避免排队等待
- 2026年4月24日正式集成到 Agents Window

#### 2. Agent Tabs：多对话并排
类似浏览器多标签页，支持多个聊天窗口并排或平铺展示。可同时监控多个 Agent 进展，随时介入或切换上下文。

#### 3. Design Mode（Cmd+Shift+D）
内置浏览器预览面板，可直接在预览界面上**圈出 UI 元素**并标注修改意图。Agent 读取标注 → 自动定位对应代码 → 直接修改。

**核心价值：** 大幅减少"描述→AI理解→定位元素→修改"的来回沟通成本，前端开发者必试。

#### 4. 30+ 插件市场
覆盖 Atlassian、Datadog、GitLab、Hugging Face 等官方插件，Agent 可直接调用：
- "把这个 bug 关联到 Jira ticket"
- "在 Datadog 上查一下这个函数的 P99 延迟"

#### 5. 多 LLM 对比模式（/best-of-n）
同一任务并行跑在多个隔离 worktree / 模型上，再比较结果。可在 Claude Sonnet、GPT-5.5、Composer 等模型间快速评估哪个更适合当前任务。

#### 6. 多仓库 + Worktree 支持
- 单个 Agent 会话可指向多个文件夹（前端、后端、shared library 三个仓库），一次任务跨 repo 修改
- 旧下拉菜单 UI 废弃，改用 `/worktree` 和 `/best-of-n` 命令
- 不同分支后台跑隔离任务，测试时一键带到本地前台

### Agents Window 工作原理
- 位置：IDE 左侧新增专属面板
- 统一管理所有正在运行的 Agent
- 覆盖环境：本地、worktree、云端、远程 SSH 环境均可进入同一个 Agents Window
- 本质变化：从"一个对话框跟 AI 说话"变为"统一调度多个 Agent 执行任务"

### 三大核心工作流

**并行执行流程：**
```
用户输入任务 → /multitask 拆解 → 多个异步子 Agent 同时启动 → 并行执行 → 汇总结果
```

**跨仓库协作流程：**
```
配置多仓库工作区 → 单个 Agent 会话 → 跨 repo 同时修改 → 无需手动切换上下文
```

**Best-of-N 评估流程：**
```
同一任务 → 并行分发到多个 worktree / 不同模型 → 各自独立执行 → 比较结果 → 挑选最优
```

### 开发者使用技巧

**如果你主要用 Tab 补全：**
- Cursor 3 影响最小，Tab 体验无变化，新功能可暂时忽略

**如果你已经在用 Agent 模式：**
- 把"一步步交代的长任务"改成用 `/multitask` "说一句，等结果"
- 在 Agents Window 里感受多 Agent 并行的体验
- 前端开发者必试 Design Mode，直接在浏览器标注需求

**如果你在团队里使用 Cursor：**
- 重点关注：云端 Agent、Jira 集成、Automations、multi-root workspace

### 成本控制建议
- 并行 Agent、best-of-n 和多仓库任务会**显著增加模型调用量**
- 启用多 Agent 前先确认账户的超额设置
- 建议对比 Copilot、Claude Code 和国产 Coding Plan 的月成本

### 范式转换对比

| 维度 | 之前版本 | Cursor 3.0 |
|------|----------|------------|
| 核心定位 | 智能文件编辑器 | Agent 调度中心 |
| 中心单元 | 文件 | Agent |
| 用户角色 | 提问者 | 调度者 |
| 交互模式 | 单对话框逐句交代 | 多 Agent 并行管理 |
| UI 修改方式 | 文字描述需求 | Design Mode 可视化标注 |
| 第三方集成 | 无 | 30+ 插件市场 |
| 模型选择 | 手动切换 | /best-of-n 自动对比 |

## 来源
- 原文链接：https://codepick.dev/zh/guides/cursor-3-new-features/
- 作者/平台：CodePick 技术博客
- 参考来源：https://cursor.com/blog/cursor-3（Cursor 官方博客）

## 标签
#效率 #工作流 #AI编程 #Cursor #多智能体 #协作 #前端开发
