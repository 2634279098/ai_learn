# Agent Skills 学习指南

> 整理自 Anthropic 官方技能库 + agentskills.io 规范

---

## 一、什么是 Agent Skills？

**Agent Skills 是一种轻量级、开放格式，用于通过专业知识和工作流程扩展 AI 智能体的能力。**

简单说：技能就是一个包含 `SKILL.md` 文件的文件夹，告诉 AI 如何以可重复的方式完成特定任务。

---

## 二、技能的标准结构

```
my-skill/
├── SKILL.md          # 必需：元数据 + 指令
├── scripts/           # 可选：可执行代码
├── references/        # 可选：文档资料
├── assets/            # 可选：模板、资源
└── ...                # 其他任意文件或目录
```

### SKILL.md 模板格式

```markdown
---
name: skill-name
description: 清晰描述技能的功能及使用场景
---

# 技能标题

[在此添加 AI 激活此技能时将遵循的指令]

## 示例
- 使用示例 1
- 使用示例 2

## 指南
- 指南 1
- 指南 2
```

**Frontmatter 字段：**
| 字段 | 说明 | 必填 |
|------|------|------|
| `name` | 唯一标识符（小写，空格用连字符） | ✅ |
| `description` | 完整描述功能和使用场景 | ✅ |

---

## 三、为什么要用 Skills？

### 解决问题

AI 智能体变得越来越强大，但往往缺乏可靠完成实际工作所需的上下文。

### Skills 的优势

| 优势 | 说明 |
|------|------|
| **封装专业知识** | 将程序性知识封装到可移植的文件夹中 |
| **版本控制** | 支持 Git 管理，记录变更历史 |
| **按需加载** | AI 可以根据任务需要动态加载对应技能 |
| **可复用** | 一次创建，多处使用 |

---

## 四、官方示例技能分类

来源：[Anthropic skills-main 仓库](https://github.com/anthropics/skills-main)

### 创意 & 设计类
| 技能 | 说明 |
|------|------|
| `algorithmic-art` | 算法艺术生成 |
| `canvas-design` | Canvas 设计 |
| `theme-factory` | 主题工厂 |
| `slack-gif-creator` | Slack GIF 制作 |

### 开发 & 技术类
| 技能 | 说明 |
|------|------|
| `claude-api/` | Claude API 多语言示例（Python/Go/Java/C#/PHP/Ruby） |
| `mcp-builder` | MCP 服务器构建 |
| `webapp-testing` | Web 应用测试 |
| `frontend-design` | 前端设计 |
| `web-artifacts-builder` | Web 工件构建 |

### 文档类
| 技能 | 说明 |
|------|------|
| `docx` | Word 文档创建与编辑 |
| `pdf` | PDF 文档处理 |
| `pptx` | PowerPoint 幻灯片制作 |
| `xlsx` | Excel 表格处理 |

### 企业 & 沟通类
| 技能 | 说明 |
|------|------|
| `brand-guidelines` | 品牌指南应用 |
| `doc-coauthoring` | 文档协作 |
| `internal-comms` | 内部沟通工作流 |
| `skill-creator` | 技能创建器（元技能） |

---

## 五、优秀设计模式借鉴

### 1. 分类组织
按领域（创意/开发/企业/文档）划分技能，便于查找和管理。

### 2. 共享模块复用
```
claude-api/
├── python/
├── go/
├── java/
└── shared/           # 共享文档
    ├── error-codes.md
    ├── agent-design.md
    └── ...
```
避免重复，提高一致性。

### 3. 多语言隔离
每种语言独立文件夹，结构清晰，便于扩展。

### 4. 元技能设计
`skill-creator` 技能本身教你如何创建技能 —— 自己教自己做。

---

## 六、Claude Code 插件安装（参考）

> 注意：这是 Anthropic Claude Code 的命令，与 WorkBuddy 不通用

### 方式一：添加市场后浏览安装
```bash
/plugin marketplace add anthropics/skills
# 然后通过菜单：Browse → anthropic-agent-skills → 选择技能 → Install
```

### 方式二：直接安装
```bash
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills
```

---

## 七、相关资源

| 资源 | 链接 |
|------|------|
| 官方规范 | [agentskills.io](https://agentskills.io) |
| 技能仓库 | [github.com/anthropics/skills-main](https://github.com/anthropics/skills-main) |
| Claude Skills 文档 | [support.claude.com](https://support.claude.com/en/articles/12512176-what-are-skills) |
| 创建自定义技能 | [How to create custom skills](https://support.claude.com/en/articles/12512198-creating-custom-skills) |

---

## 八、实践建议

### 创建技能时
1. **描述要精准** → 让 AI 知道何时该调用
2. **示例要具体** → 提供实际使用场景
3. **指令要清晰** → 步骤明确，避免歧义

### 组织技能时
1. **按领域分类** → 便于管理和查找
2. **共享模块抽取** → 避免重复代码
3. **版本控制** → 用 Git 管理技能仓库

### 迭代优化时
1. **记录使用反馈** → 哪些指令有效/无效
2. **持续完善示例** → 补充更多实际场景
3. **定期审查** → 移除过时内容

---

*最后更新：2026-05-17*
