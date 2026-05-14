# Claude Prompt工程最佳实践

## 一句话总结
Anthropic官方发布的Claude 4.x系列模型提示词工程系统指南，通过结构化输入、行为约束和参数调优，最大化Claude在代码生成、长文档分析、Agent工作流等场景的表现。

## 适用场景
- 使用Claude进行AI辅助编程、代码审查
- 基于Claude构建AI Agent、自动化工作流
- 处理长文档分析、复杂推理任务
- 需要稳定、可复现的Claude输出结果

## 详细内容
### 一、核心原则（五条黄金法则）
1. **把Claude当作"缺上下文的新同事"**：隐性的需求要显性化，避免让模型"猜"意图
   ```markdown
   ❌ 模糊版：Create an analytics dashboard
   ✅ 清晰版：Create an analytics dashboard. Include as many relevant features and interactions as possible. Go beyond the basics to create a fully-featured implementation.
   ```
2. **给指令附加"为什么"的理由**：提升规则的泛化能力，避免模型机械遵守
   ```markdown
   ❌ 效果较差：NEVER use ellipses
   ✅ 效果更好：Your response will be read aloud by a text-to-speech engine, so never use ellipses since the text-to-speech engine will not know how to pronounce them.
   ```
3. **Few-shot示例原则**：3~5个示例效果最佳，示例要贴近真实场景、覆盖边缘情况，用`<example>`标签结构化
4. **XML标签结构化输入**：给模型可解析的文档树，尤其适合多文档、多轮对话场景
   ```xml
   <documents>
     <document index="1">
       <source>annual_report_2023.pdf</source>
       <document_content>{{ANNUAL_REPORT}}</document_content>
     </document>
   </documents>
   Analyze the annual report and identify strategic advantages.
   ```
5. **精准角色设定**：在system prompt中设定专业角色，避免过于戏剧化的描述

### 二、关键实用技巧
1. **长上下文排版优化**：文档内容放在顶部、指令放在末尾，可提升约30%的输出质量；要求模型先用`<relevant_quotes>`标签引用原文片段，再在`<answer>`中给出回答
2. **控制输出冗长度**：用正面指令`Provide concise, focused responses`替代负面指令`Do not be verbose`
3. **抑制过度Markdown**：通过结构化标签约束输出格式，避免无意义的列表、加粗，优先使用完整段落
4. **`effort`参数调优（Opus 4.7+）**：
   | 档位 | 适用场景 |
   |------|---------|
   | `xhigh` | 编码、Agent类任务（推荐） |
   | `high` | 智能敏感场景（最低推荐档位） |
   | `medium` | 成本敏感场景（默认） |
5. **工具调用优化**：独立工具调用尽量并行，依赖调用串行，提升执行效率

### 三、Agent工作流治理
1. **状态持久化**：上下文窗口接近上限时，将进度、状态保存到文件或git，避免任务中断
2. **行动可逆性分级**：
   - 可直接执行：本地文件编辑、运行测试
   - 需确认后执行：删除文件、git push --force、删除数据库表
3. **防止过度工程**：只实现明确要求的功能，不主动添加未要求的特性、注释或抽象

### 四、Opus 4.7版本适配要点
1. 指令更字面化，需显式说明规则适用范围（如`apply to every section, not just the first one`）
2. 不再支持预填充响应，需用Structured Outputs或工具调用替代
3. 默认前端审美偏米色底+衬线字体，可通过`<frontend_aesthetics>`标签自定义

## 来源
- 原文链接：https://www.iceyao.com.cn/2026/04/25/claude-prompting-best-practices/
- 作者/平台：Anthropic官方最佳实践解读

## 标签
#AI编程 #Prompt工程 #Claude #Agent工作流 #开发效率
