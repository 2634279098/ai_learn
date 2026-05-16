# 谷歌 Agent Skills 工程化实践

## 一句话总结
谷歌 Gemini 团队开源的 Agent Skills 项目提供 20 个结构化工程技能，将资深工程师的开发标准封装为可复用工作流，让 AI 编程助手在每个开发阶段都能遵循生产级规范，解决 AI 代码质量不稳定、缺少测试、安全漏洞等核心痛点。

## 适用场景
- 使用 AI 辅助编程但输出质量不稳定，希望提升 AI 生成代码的工程标准
- 团队希望统一 AI 辅助开发的流程规范，避免 AI "走捷径"跳过关键环节
- 在 Claude Code、Cursor、Gemini CLI 等 AI 编程工具中需要系统化的技能库
- 希望将 Google 内部工程文化（测试金字塔、基于主干开发、左移理念等）融入 AI 工作流

## 详细内容

### 一、项目背景

**Agent Skills** 由谷歌 Gemini 团队主管 **Addy Osmani** 开源，短时间内获得 **1.9 万+ Star**。

#### 解决的痛点
- ❌ 代码质量参差不齐
- ❌ 缺少测试覆盖
- ❌ 安全漏洞频发
- ❌ 文档不规范
- ❌ AI 倾向于"走捷径"，跳过需求文档、测试、安全评审等关键环节

**核心理念**：Agent Skills 不是新框架也不是新模型，而是一套完整的**工程化工作流**，将谷歌内部工程实践和资深工程师的多年经验封装成结构化可复用技能。

> GitHub 地址：https://github.com/addyosmani/agent-skills

---

### 二、覆盖的 6 大开发阶段

| 阶段 | 英文 | 核心目标 |
|------|------|----------|
| 定义 | Define | 把模糊想法变成具体提案，输出完整需求文档 |
| 规划 | Plan | 把需求拆解成小的、可验证的原子化任务 |
| 构建 | Build | 增量式实现、测试驱动开发、上下文工程 |
| 验证 | Verify | 浏览器测试、调试与错误恢复 |
| 评审 | Review | 代码评审、简化、安全加固、性能优化 |
| 发布 | Ship | Git 工作流、CI/CD、废弃迁移、文档、发布上线 |

---

### 三、20 个核心技能详解

#### 🔵 定义阶段（2 个技能）

**1. `idea-refine`**
- 结构化的发散/收敛思考流程
- 将模糊的想法转化为具体可执行的提案

**2. `spec-driven-development`**
- 先写需求文档，再写代码
- 覆盖目标、命令、结构、代码风格、测试、边界等完整维度

---

#### 🟡 规划阶段（1 个技能）

**3. `planning-and-task-breakdown`**
- 将需求拆解成小的可验证任务
- 带有验收标准和依赖顺序，确保任务可追踪

---

#### 🟠 构建阶段（5 个技能）

**4. `incremental-implementation`**
- 薄垂直切片开发模式
- 实现 → 测试 → 验证 → 提交的循环
- 支持特性标志、安全默认值、回滚友好设计

**5. `test-driven-development`**
- 红-绿-重构（TDD 经典流程）
- 测试金字塔：**80% 单元 / 15% 集成 / 5% E2E**
- 遵循 DAMP 原则（优于 DRY）和 Beyonce 规则（"如果你喜欢它，就应该为它写测试"）

**6. `source-driven-development`**
- 基于官方文档做框架决策
- 验证、引用来源、标记未验证内容，避免 AI 幻觉

**7. `frontend-ui-engineering`**
- 组件架构与设计系统
- 状态管理、响应式设计
- 遵循 **WCAG 2.1 AA** 无障碍标准

**8. `api-and-interface-design`**
- 契约优先设计
- 遵循 **Hyrum 定律**（隐式依赖管理）
- 一版本规则、错误语义、边界验证

---

#### 🟢 验证阶段（2 个技能）

**9. `browser-testing-with-devtools`**
- 集成 Chrome DevTools MCP 获取实时运行时数据
- 涵盖 DOM 检查、控制台日志、网络跟踪、性能分析

**10. `debugging-and-error-recovery`**
- **五步分类法**：复现 → 定位 → 简化 → 修复 → 防护
- 停机规则与安全回退机制

---

#### 🔴 评审阶段（4 个技能）

**11. `code-review-and-quality`**
- 五轴评审体系
- 变更大小控制（约 100 行）
- 严重性标签：Nit / Optional / FYI
- 评审速度规范与拆分策略

**12. `code-simplification`**
- 遵循 **Chesterton 栅栏原则**（不理解就不删除）
- **500 规则**（单文件不超过 500 行）
- 在保留精确行为的同时降低复杂度

**13. `security-and-hardening`**
- **OWASP Top 10** 防护
- 认证模式、密钥管理、依赖审计
- 三层边界系统

**14. `performance-optimization`**
- 先测量，再优化（避免过早优化）
- 核心 Web 指标（Core Web Vitals）目标
- 分析工作流、包分析、反模式检测

---

#### 🟣 发布阶段（6 个技能）

**15. `git-workflow-and-versioning`**
- 基于主干开发（Trunk-Based Development）
- 原子提交，变更大小约 100 行
- 提交即保存点模式

**16. `ci-cd-and-automation`**
- 左移（Shift Left）理念
- 越快越安全原则
- 特性标志、质量门禁流水线、失败反馈循环

**17. `deprecation-and-migration`**
- **代码即负债**思维
- 强制与建议废弃策略
- 迁移模式与僵尸代码移除

**18. `documentation-and-adrs`**
- 架构决策记录（ADR）
- API 文档与内联文档标准
- 重点记录"为什么"而非"是什么"

**19. `shipping-and-launch`**
- 发布前检查清单
- 特性标志生命周期管理
- 分阶段推出、回滚流程、监控设置

**20. `context-engineering`**
- 上下文工程，确保 AI 在正确的信息范围内工作

---

### 四、7 个触发命令（一键激活）

| 命令 | 用途 | 核心理念 |
|------|------|----------|
| `/spec` | 定义要构建什么 | 先写需求再写代码 |
| `/plan` | 规划如何构建 | 小的原子化任务 |
| `/build` | 增量式构建 | 一次只做一块 |
| `/test` | 证明它能工作 | 测试就是证明 |
| `/review` | 合并前评审 | 提高代码健康度 |
| `/code-simplify` | 简化代码 | 清晰胜过聪明 |
| `/ship` | 发布到生产 | 越快越安全 |

> 💡 命令会根据上下文智能激活技能，例如设计 API 时自动触发 API 设计技能，构建 UI 时自动触发前端工程技能。

---

### 五、融入的 Google 工程文化精髓

- **Hyrum 定律**：API 设计中管理隐式依赖
- **Beyonce 规则**：测试中"如果你喜欢它，就应该为它写测试"
- **测试金字塔**：80/15/5 的测试分层比例
- **Chesterton 栅栏**：不理解原因就不删除代码
- **基于主干开发**：避免长期分支带来的集成地狱
- **左移理念**：在 CI/CD 中尽早发现问题
- **代码即负债**：专门的废弃技能，主动管理技术债

---

### 六、如何使用（快速上手）

#### Claude Code（推荐）
```bash
# Marketplace 安装
/plugin marketplace add addyosmani/agent-skills
/plugin install agent-skills@addy-agent-skills

# 本地安装
git clone https://github.com/addyosmani/agent-skills.git
claude --plugin-dir /path/to/agent-skills
```

#### Cursor
将任意 `SKILL.md` 文件复制到 `.cursor/rules/` 目录，或引用完整的 `skills/` 目录。

#### Gemini CLI
```bash
# 从仓库安装
gemini skills install https://github.com/addyosmani/agent-skills.git --path skills

# 从本地克隆安装
gemini skills install ./agent-skills/skills/
```

#### 其他工具（Codex 等）
由于技能文件均为**纯 Markdown 格式**，可在任何支持系统提示或指令文件的 AI 代理上直接使用。

---

### 七、实际效果

Agent Skills 将"怎样才算好代码"这个主观问题，转化为**可执行、可验证的工作流**：

- ✅ 每个开发环节都有明确步骤和检查点
- ✅ AI 被强制遵循生产级标准，不再走捷径
- ✅ 输出质量更稳定，减少返工
- ✅ 安全、性能、可维护性全面提升
- ✅ 从需求到上线，全流程有据可查

## 来源
- 原文链接：https://cloud.tencent.com/developer/article/2658842
- 作者/平台：腾讯云开发者社区（谷歌 Gemini 团队开源项目解读）

## 标签
#效率 #工作流 #AI编程 #Google #AgentSkills #工程化 #TDD #代码质量
