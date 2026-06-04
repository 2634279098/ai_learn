# OpenAI Codex Skills 生产工作流最佳实践

## 一句话总结
Codex Skills 将可重用的 AI 工作流打包为可版本控制、可团队共享的模块，解决 AI 编码中行为不一致、重复提示工程和知识孤岛三大痛点。

## 适用场景
- 团队中有重复性开发工作流（部署、迁移、测试生成等）需要 AI 自动化
- 多人协作需要统一 AI 代理行为
- 需要将"部落知识"转化为可复用、可审计的标准化流程
- 希望 AI 执行确定性的重复任务，而非每次可能产生不同结果

## 详细内容

### 什么是 Codex Skills
OpenAI Codex Skills 于 2025 年 12 月作为实验性功能推出，在 2026 年已成为最重要的面向开发者的能力之一。Skills 将可重用的工作流（指令、脚本、参考资料）打包在一起，让 Codex 每次都能以相同的方式执行重复性任务。

### Skills 解决的核心问题

| 问题 | 没有 Skills | 有 Skills |
|------|-------------|-----------|
| 代理行为不一致 | 相同提示，不同结果 | Skills 强制执行分步工作流 |
| 重复的提示工程 | 每次都重写提示 | 编写一次，永远调用 |
| 知识孤岛 | 存在于头脑中的部落知识 | Skills 是版本控制和共享的 |

### Skill 目录结构

```
my-skill/
├── SKILL.md       # 必需：指令和元数据
├── scripts/       # 可选：辅助脚本
│   ├── deploy.sh
│   └── rollback.sh
├── references/    # 可选：文档、示例
│   ├── api-spec.md
│   └── examples.json
└── tests/         # 可选：skill 验证
    └── test-cases.md
```

### 必需的 Frontmatter 配置

```yaml
---
name: deploy-to-staging
description: Deploys current branch to staging with health checks - use when user says "deploy to staging", "push to staging", or "test on staging"
---
```

**关键：description 字段控制隐式调用**——Codex 用它来决定是否自动调用 skill。使用开发者实际使用的确切词语。

### 推荐的 SKILL.md 模板

```markdown
---
name: <one-job-skill-name>
description: <user-language description with trigger phrases>
---

## When to Use This Skill

<2-3 sentences on when this applies>

## Steps

1. <Specific actionable step>
2. <Next step>
3. <Final step>

## Inputs

- <input-name>: <description and constraints>

## Outputs

- <output-name>: <what this produces>

## References

- See `./references/api-spec.md` for the API contract
- See `./scripts/deploy.sh` for the deployment script
```

### 十大最佳实践

#### #1：将每个 Skill 限制为一个任务
不要创建"全流程"的巨型 Skill，而是拆分为可组合的小 Skill：
- `build-and-test`、`deploy-to-staging`、`notify-team`

#### #2：编写与用户语言匹配的描述
- ❌ `Initiates CI/CD orchestration with branch promotion to non-production environment`
- ✅ `Deploys current branch to staging - use when user says "deploy to staging"`

#### #3：定义清晰的输入和输出
将 Skills 视为函数，指定输入约束和产出格式：

```markdown
## Inputs
- target-environment: "staging" or "production" (required)
- skip-tests: boolean (optional, default: false)

## Outputs
- deploy-url: The URL of the deployed environment
- deploy-duration-seconds: Time taken to deploy
- error-message: Present only if deploy failed
```

#### #4：从真实用例开始
推荐的 10 大核心 Skills：
1. `deploy-to-staging` — 部署到 staging
2. `run-database-migration` — 安全运行数据库迁移
3. `generate-pr-description` — 从提交自动编写 PR 描述
4. `update-changelog` — 从提交更新 CHANGELOG.md
5. `create-feature-branch` — 创建分支 + 设置 + 初始提交
6. `add-test-coverage` — 为未测试的函数添加测试
7. `refactor-deprecated-api` — 从旧 API 迁移到新 API
8. `setup-new-package` — 构建新的内部包
9. `audit-security` — 运行安全检查 + 报告
10. `update-dependencies` — 升级依赖项 + 运行测试

#### #5：使用渐进式披露获取上下文
Codex 首先加载每个 Skill 的名称和描述，选择后才加载完整的 SKILL.md。**描述至关重要，SKILL.md 可以详细。**

#### #6：对 Skills 进行版本控制
像对待代码一样对待 Skills——提交到 git，通过 PR 审查更改。

```bash
# 团队共享
ln -s ~/team-skills/skills ~/.codex/skills/team
```

#### #7：在共享前测试
测试清单：干净仓库工作、隐式调用正确触发、输入处理边缘情况、输出一致、错误消息可操作。

对高风险 Skills 添加试运行模式：
```yaml
## Inputs
- dry-run: boolean (default: false) - If true, print actions without executing
```

#### #8：优化 Skill 执行成本

| 模型 | 输入$/百万Token | 输出$/百万Token | 最适合 |
|------|----------------|-----------------|--------|
| GPT-4.1 Nano | $0.10 | $0.40 | 便宜，大批量 |
| GPT-4.1 Mini | $0.40 | $1.60 | 大多数工作流 |
| GPT-4.1 | $2.00 | $8.00 | 标准推理 |
| GPT-5 | $5.00 | $25.00 | 复杂推理 |
| o3 | $10.00 | $40.00 | 深入推理 |

#### #9：使 Skills 可发现
- README.md 列出每个 Skill
- `/skills list` 斜杠命令目录
- 入职文档包含 Skills 用法

#### #10：基于失败迭代
| 失败模式 | 可能原因 |
|---------|---------|
| Codex 未调用应匹配的 Skill | 描述过于抽象 |
| 调用了错误的 Skill | 描述与其他 Skill 重叠 |
| 执行但产生错误输出 | 步骤不清楚或不完整 |
| 中途失败 | 缺少错误处理或输入 |

### 显式调用与隐式调用
- 显式：`$.my-skill` — 直接调用
- 隐式：Codex 根据 description 自动匹配触发

## 来源
- 原文链接：https://www.getaiperks.com/zh/blogs/36-codex-skills-best-practices-2026
- 作者/平台：AIPerks 技术博客
- 参考来源：OpenAI Codex 官方文档

## 标签
#效率 #工作流 #AI编程 #OpenAI #Codex #自动化 #团队协作
