# Function Calling 完整指南（GPT/Claude/Gemini 对比）

## 一句话总结
Function Calling（Tool Use）让 LLM 能够调用外部函数与真实世界交互，本文全面对比 OpenAI、Anthropic、Google 三大平台的格式差异、API 调用方式和生产最佳实践。

## 适用场景
- 需要让 LLM 调用外部 API、数据库、工具的开发者
- 构建 AI Agent 需要多步骤工具编排的场景
- 希望在 GPT/Claude/Gemini 之间切换或同时支持多个模型的团队
- 需要生产级 Function Calling 实现（错误处理、并行调用、成本控制）的应用

## 详细内容

---

### 一、什么是 Function Calling？

Function Calling（也称 Tool Use）是一种让 LLM **调用外部函数**的机制。你向模型描述可用的函数（工具），模型决定何时以及如何调用它们。

> ⚠️ **关键点**：模型本身**不执行**函数，它只返回结构化的调用参数，由你的代码执行后再将结果反馈给模型。

**为什么重要？** 没有 Function Calling，LLM 只能依赖训练数据；有了它，可以：
- 查询实时数据（天气、股价、数据库）
- 执行操作（发邮件、创建工单）
- 使用专用工具（计算器、搜索引擎）
- 链式操作（多步骤工作流）

---

### 二、工作原理（通用循环）

```
用户消息 → LLM决定调用函数 → 你的代码执行函数
→ 结果返回给LLM → LLM生成最终回复
```

**六步标准流程：**

| 步骤 | 操作 |
|------|------|
| 1 | **定义工具**：描述函数名、参数、用途 |
| 2 | **发送消息+工具**：API调用时附带工具定义 |
| 3 | **检测工具调用**：判断模型是否要调用函数 |
| 4 | **执行函数**：用模型返回的参数运行你的代码 |
| 5 | **返回结果**：将函数输出发回给模型 |
| 6 | **获取最终回复**：模型利用结果回答用户 |

---

### 三、三大主流模型差异对比

#### 工具定义格式对比

| 维度 | OpenAI (GPT) | Anthropic (Claude) | Google (Gemini) |
|------|-------------|-------------------|-----------------|
| **外层包装** | `{"type": "function", "function": {...}}` | 直接对象（无包装） | `types.Tool(function_declarations=[...])` |
| **Schema键名** | `parameters` | `input_schema` | `parameters`（`types.Schema`对象） |
| **Schema格式** | JSON Schema | JSON Schema | Protocol Buffer 类型 |
| **最大工具数** | 128 | 128 | 128 |

#### API 调用格式对比

| 维度 | OpenAI | Anthropic | Gemini |
|------|--------|-----------|--------|
| **工具参数** | `tools=[...]` | `tools=[...]` | `config.tools=[...]` |
| **工具选择控制** | `tool_choice` | `tool_choice` | `tool_config.function_calling_config` |
| **强制指定工具** | `{"type":"function","function":{"name":"X"}}` | `{"type":"tool","name":"X"}` | `mode="ANY", allowed_function_names=["X"]` |
| **强制调用任意工具** | `"required"` | `{"type":"any"}` | `mode="ANY"` |
| **禁用工具** | `"none"` | 省略tools参数 | `mode="NONE"` |

#### 响应格式对比

| 维度 | OpenAI | Anthropic | Gemini |
|------|--------|-----------|--------|
| **工具调用位置** | `message.tool_calls[]` | `content[]`块（`type="tool_use"`） | `parts[]`（含`function_call`属性） |
| **检测方式** | 检查`message.tool_calls` | 检查`stop_reason == "tool_use"` | 检查parts中是否有`function_call` |
| **参数类型** | JSON字符串（需解析） | Dict（已解析） | Proto对象（用`dict()`转换） |
| **调用ID** | `tool_call.id` | `block.id` | 无（按函数名匹配） |

#### 工具结果格式对比

| 维度 | OpenAI | Anthropic | Gemini |
|------|--------|-----------|--------|
| **角色** | `"tool"` | `"user"` | `"user"`（作为parts） |
| **ID引用** | `tool_call_id` | `tool_use_id` | 函数名 |
| **内容类型** | 字符串 | 字符串 | Dict |
| **错误处理** | 返回错误字符串 | 返回`is_error: true`块 | 在响应dict中返回错误 |

---

### 四、核心代码示例

#### OpenAI GPT 实现

```python
from openai import OpenAI
import json

client = OpenAI()

# 定义工具
tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "description": "获取指定地点的当前天气",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {"type": "string", "description": "城市和国家"},
                "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]}
            },
            "required": ["location"]
        }
    }
}]

def chat_with_tools(user_message: str) -> str:
    messages = [{"role": "user", "content": user_message}]
    
    # 第一次调用
    response = client.chat.completions.create(
        model="gpt-5.4",
        messages=messages,
        tools=tools,
        tool_choice="auto",
    )
    assistant_message = response.choices[0].message
    
    if assistant_message.tool_calls:
        messages.append(assistant_message)
        for tool_call in assistant_message.tool_calls:
            func_name = tool_call.function.name
            func_args = json.loads(tool_call.function.arguments)  # ⚠️ 需要JSON解析
            result = AVAILABLE_FUNCTIONS[func_name](**func_args)
            messages.append({
                "role": "tool",
                "tool_call_id": tool_call.id,
                "content": json.dumps(result),
            })
        # 第二次调用获取最终回复
        final = client.chat.completions.create(model="gpt-5.4", messages=messages, tools=tools)
        return final.choices[0].message.content
    
    return assistant_message.content
```

#### Anthropic Claude 实现

```python
import anthropic
import json

client = anthropic.Anthropic()

# Claude格式工具定义（注意：无外层包装，用input_schema）
tools = [{
    "name": "get_weather",
    "description": "获取指定地点的当前天气",
    "input_schema": {  # ⚠️ 关键差异：用input_schema而非parameters
        "type": "object",
        "properties": {
            "location": {"type": "string"},
            "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]}
        },
        "required": ["location"]
    }
}]

def chat_with_tools_claude(user_message: str) -> str:
    messages = [{"role": "user", "content": user_message}]
    response = client.messages.create(
        model="claude-sonnet-4.6",
        max_tokens=1024,
        tools=tools,
        messages=messages,
    )
    
    if response.stop_reason == "tool_use":  # ⚠️ 检测方式不同
        messages.append({"role": "assistant", "content": response.content})
        tool_results = []
        for block in response.content:
            if block.type == "tool_use":
                result = AVAILABLE_FUNCTIONS[block.name](**block.input)  # ⚠️ input已是dict
                tool_results.append({
                    "type": "tool_result",
                    "tool_use_id": block.id,
                    "content": json.dumps(result),
                })
        messages.append({"role": "user", "content": tool_results})  # ⚠️ 结果放在user角色
        final = client.messages.create(
            model="claude-sonnet-4.6", max_tokens=1024,
            tools=tools, messages=messages
        )
        return "".join(b.text for b in final.content if b.type == "text")
    
    return "".join(b.text for b in response.content if b.type == "text")
```

#### Google Gemini 实现

```python
from google import genai
from google.genai import types

client = genai.Client()

# Gemini使用Protocol Buffer风格类型
get_weather_declaration = types.FunctionDeclaration(
    name="get_weather",
    description="获取指定地点的当前天气",
    parameters=types.Schema(
        type=types.Type.OBJECT,
        properties={
            "location": types.Schema(type=types.Type.STRING),
            "unit": types.Schema(type=types.Type.STRING, enum=["celsius", "fahrenheit"]),
        },
        required=["location"],
    ),
)
gemini_tools = types.Tool(function_declarations=[get_weather_declaration])

def chat_with_tools_gemini(user_message: str) -> str:
    response = client.models.generate_content(
        model="gemini-2.0-flash",
        contents=user_message,
        config=types.GenerateContentConfig(tools=[gemini_tools]),
    )
    parts = response.candidates[0].content.parts
    function_call_parts = [p for p in parts if p.function_call]
    
    if function_call_parts:
        function_responses = []
        for part in function_call_parts:
            fc = part.function_call
            result = AVAILABLE_FUNCTIONS[fc.name](**dict(fc.args))
            function_responses.append(
                types.Part.from_function_response(name=fc.name, response=result)
            )
        contents = [
            types.Content(role="user", parts=[types.Part.from_text(text=user_message)]),
            response.candidates[0].content,
            types.Content(role="user", parts=function_responses),
        ]
        final = client.models.generate_content(
            model="gemini-2.0-flash", contents=contents,
            config=types.GenerateContentConfig(tools=[gemini_tools])
        )
        return final.text
    return response.text
```

---

### 五、Agent Loop（多步骤工具链）

适用于需要多次顺序调用工具的场景（如旅行规划：先查用户偏好→搜索航班→搜索酒店→推荐）：

```python
def run_agent(user_message: str, max_iterations: int = 10) -> str:
    messages = [{"role": "system", "content": "你是旅行助手..."}, 
                {"role": "user", "content": user_message}]
    
    for iteration in range(max_iterations):
        response = client.chat.completions.create(
            model="gpt-5.4", messages=messages, tools=agent_tools
        )
        assistant_message = response.choices[0].message
        messages.append(assistant_message)
        
        if not assistant_message.tool_calls:
            return assistant_message.content  # 完成，返回最终回复
        
        for tool_call in assistant_message.tool_calls:
            result = AGENT_FUNCTIONS[tool_call.function.name](
                **json.loads(tool_call.function.arguments)
            )
            messages.append({
                "role": "tool",
                "tool_call_id": tool_call.id,
                "content": json.dumps(result),
            })
    
    return "已达最大迭代次数"
```

**生产环境安全防护：**
- ✅ `max_iterations` 限制最大迭代次数
- ✅ `max_total_tokens` 限制总Token消耗
- ✅ `timeout_seconds` 超时控制
- ✅ `allowed_functions` 白名单限制可调用函数
- ✅ `require_confirmation` 高危操作需用户确认

---

### 六、并行工具调用

当用户问"东京和伦敦的天气？"时，模型可一次返回多个工具调用，用线程池并行执行：

```python
from concurrent.futures import ThreadPoolExecutor

if assistant_message.tool_calls:
    def execute_tool(tool_call):
        result = AVAILABLE_FUNCTIONS[tool_call.function.name](
            **json.loads(tool_call.function.arguments)
        )
        return tool_call.id, result
    
    with ThreadPoolExecutor() as executor:
        futures = [executor.submit(execute_tool, tc) for tc in assistant_message.tool_calls]
        results = [f.result() for f in futures]
```

> 禁用并行调用（OpenAI）：`parallel_tool_calls=False`

---

### 七、错误处理要点

| 场景 | 处理方式 |
|------|---------|
| 函数不存在 | 返回`{"error": "Function not found"}` |
| JSON参数解析失败 | `try/except json.JSONDecodeError` |
| 函数执行异常 | `try/except Exception` 捕获并返回错误信息 |
| Claude专用错误 | 返回`{"is_error": true, "content": "错误信息"}` |
| 超时 | 使用`signal.SIGALRM`装饰器设置超时 |
| 无限循环 | `LoopDetector`检测相同参数重复调用≥3次 |

---

### 八、生产最佳实践（8条核心原则）

| # | 原则 | 要点 |
|---|------|------|
| 1 | **写清晰的工具描述** | 明确说明何时使用、返回什么，避免模糊描述 |
| 2 | **最小化工具数量** | 每个工具定义消耗Token，按需动态过滤工具 |
| 3 | **验证和清理工具输出** | 截断过长结果（建议≤5000字符），避免浪费Token |
| 4 | **记录完整日志** | 记录函数名、参数、结果长度、延迟、时间戳 |
| 5 | **用Pydantic做类型安全** | 定义请求模型，在执行前验证参数合法性 |
| 6 | **构建统一抽象层** | 使用如Ofox.ai的聚合网关，一套代码兼容所有模型 |
| 7 | **系统化测试工具定义** | 用pytest验证工具选择准确性和参数提取正确性 |
| 8 | **精细化成本监控** | 追踪每次Agent执行的总Token和费用，而非单次API调用 |

**成本参考（每百万Token）：**
| 模型 | 输入 | 输出 |
|------|------|------|
| GPT-5.4 | $2.00 | $8.00 |
| Claude Sonnet 4.6 | $3.00 | $15.00 |
| Gemini 2.0 Flash | $0.50 | $3.00 |

---

### 九、总结

> Function Calling 将 LLM 从文本生成器转变为能与真实世界交互的智能体。核心模式在各提供商间一致——**定义工具 → 检测调用 → 执行函数 → 返回结果**——但 OpenAI、Anthropic、Google 之间的格式差异需要仔细处理。建议从单个工具和简单循环开始，逐步添加错误处理和成本追踪，再扩展功能。

## 来源
- 原文链接：https://ofox.ai/blog/function-calling-tool-use-complete-guide-2026/
- 作者/平台：Ofox AI

## 标签
#效率 #工作流 #AI编程 #FunctionCalling #ToolUse #GPT #Claude #Gemini #多模型对比
