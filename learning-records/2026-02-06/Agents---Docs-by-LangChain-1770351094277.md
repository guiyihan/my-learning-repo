# Agents - Docs by LangChain

> 📅 记录时间: 2026/2/6 12:11:34
> 🔗 来源: [Agents - Docs by LangChain](https://docs.langchain.com/oss/python/langchain/agents)

## AI总结

# LangChain Agents 文档分析总结

## 1. 核心主题

这是 LangChain 框架中关于 **Agents（智能代理）** 的官方文档，主要介绍如何使用 `create_agent` 函数创建能够自主推理、选择工具并迭代解决问题的 AI 系统。Agents 结合了语言模型和工具，基于 LangGraph 构建图结构的运行时环境。

## 2. 关键要点

### ⭐ Agent 的核心机制
- **ReAct 模式**：Agent 遵循"推理+行动"循环模式（Reasoning + Acting）
- 工作流程：接收输入 → 模型推理 → 调用工具 → 观察结果 → 继续推理 → 输出最终答案
- 基于图结构（节点+边）运行，包括模型节点、工具节点和中间件

### ⭐ 三大核心组件

1. **Model（模型）**
   - 静态模型：创建时配置，执行期间不变
   - 动态模型：基于上下文运行时选择（如根据对话复杂度切换）

2. **Tools（工具）**
   - 支持顺序调用、并行调用
   - 内置错误处理和重试逻辑
   - 可动态过滤或注册工具

3. **System Prompt（系统提示）**
   - 可静态配置或通过中间件动态生成
   - 支持高级功能（如 Anthropic 的提示缓存）

### ⭐ 高级特性

- **结构化输出**：通过 `ToolStrategy` 或 `ProviderStrategy` 强制特定格式输出
- **记忆管理**：自动维护对话历史，支持自定义状态模式
- **中间件系统**：用于动态模型选择、工具过滤、错误处理等

### ⭐ 灵活的配置方式

- 支持模型标识符字符串（如 `"openai:gpt-5"`）
- 支持直接实例化模型类以精确控制参数
- 工具可用普通 Python 函数或协程定义

## 3. 重要细节

### 🔧 技术细节
- **工具装饰器**：使用 `@tool` 装饰器自定义工具属性
- **中间件装饰器**：
  - `@wrap_model_call`：包装模型调用
  - `@wrap_tool_call`：处理工具执行
  - `@dynamic_prompt`：动态生成系统提示

### ⚠️ 注意事项
- 使用结构化输出时，不支持预绑定工具的模型（pre-bound models）
- 空工具列表会创建仅含 LLM 节点的 agent（无工具调用能力）
- 自定义状态模式必须继承 `AgentState` 作为 `TypedDict`

### 📊 代码示例模式
```python
# 基础创建
agent = create_agent("openai:gpt-5", tools=tools)

# 带中间件的高级配置
agent = create_agent(
    model=model,
    tools=tools,
    system_prompt="...",
    middleware=[custom_middleware],
    response_format=ProviderStrategy(Schema)
)

# 调用
result = agent.invoke({
    "messages": [{"role": "user", "content": "query"}]
})
```

## 4. 个人学习价值

### 💡 技术启发

1. **模块化设计思想**：模型、工具、提示词三者解耦，通过中间件灵活组合，是优秀的架构设计范例

2. **ReAct 模式理解**：这是构建自主 AI 系统的核心范式，理解"观察-推理-行动"循环对开发智能应用至关重要

3. **动态配置能力**：学会在运行时根据上下文动态调整模型、工具和提示词，可显著提升系统的适应性和成本效益

### 🎯 实践应用场景

- **企业级应用**：权限控制（动态工具过滤）、多租户场景（动态提示词）
- **成本优化**：根据任务复杂度切换不同价位的模型
- **可靠性提升**：统一的错误处理、工具重试机制

### 📚 学习建议

1. 先掌握基础用法（静态模型+简单工具）
2. 深入理解 ReAct 循环的执行流程
3. 逐步学习中间件系统实现高级功能
4. 关注结构化输出在实际业务中的应用

这份文档是构建生产级 AI Agent 的完整指南，值得反复研读并结合实际项目实践。

## 原文摘录

Skip to main contentDocs by LangChain home pageOpen sourceSearch...NavigationCore componentsAgentsCore componentsAgentsCopy pageAgents combine language models with tools to create systems that can reason about tasks, decide which tools to use, and iteratively work towards solutions. create_agent provides a production-ready agent implementation. An LLM Agent runs tools in a loop to achieve a goal. An agent runs until a stop condition is met - i.e., when the model emits a final output or an iteration limit is reached. actionobservationfinishinputmodeltoolsoutput create_agent builds a graph-based agent runtime using LangGraph. A graph consists of nodes (steps) and edges (connections) that define how your agent processes information. The agent moves through this graph, executing nodes like the model node (which calls the model), the tools node (which executes tools), or middleware.Learn more about the Graph API. ​Core components ​Model The model is the reasoning engine of your agent. It can be specified in multiple ways, supporting both static and dynamic model selection. ​Static model Static models are configured once when creating the agent and remain unchanged throughout execution. This is the most common and straightforward approach. To initialize a static model from a model identifier string: Copyfrom langchain.agents import create_agent agent = create_agent("openai:gpt-5", tools=tools) Model identifier strings support automatic inference (e.g., "gpt-5" will be inferred as "openai:gpt-5"). Refer to the reference to see a full list of model identifier string mappings. For more control over the model configuration, initialize a model instance directly using the provider package. In this example, we use ChatOpenAI. See Chat models for other available chat model classes. Copyfrom langchain.agents import create_agent from langchain_openai import ChatOpenAI model = ChatOpenAI( model="gpt-5", temperature=0.1, max_tokens=1000, timeout=30 # ... (other params) ) agent = create_agent(model, tools=tools) Model instances give you complete control over configuration. Use them when you need to set specific parameters like temperature, max_tokens, timeouts, base_url, and other provider-specific settings. Refer to the reference to see available params and methods on your model. ​Dynamic model Dynamic models are selected at runtime based on the current state and context. This enables sophisticated routing logic and cost optimization. To use a dynamic model, create middleware using the @wrap_model_call decorator that modifies the model in the request: Copyfrom langchain_openai import ChatOpenAI from langchain.agents import create_agent from langchain.agents.middleware import wrap_model_call, ModelRequest, ModelResponse basic_model = ChatOpenAI(model="gpt-4.1-mini") advanced_model = ChatOpenAI(model="gpt-4.1") @wrap_model_call def dynamic_model_selection(request: ModelRequest, handler) -> ModelResponse: """Choose model based on conversation complexity.""" message_count = len(request.state["messages"]) if message_count > 10: # Use an advanced model for longer conversations model = advanced_model else: model = basic_model return handler(request.override(model=model)) agent = create_agent( model=basic_model, # Default model tools=tools, middleware=[dynamic_model_selection] ) Pre-bound models (models with bind_tools already called) are not supported when using structured output. If you need dynamic model selection with structured output, ensure the models passed to the middleware are not pre-bound. For model configuration details, see Models. For dynamic model selection patterns, see Dynamic model in middleware. ​Tools Tools give agents the ability to take actions. Agents go beyond simple model-only tool binding by facilitating: Multiple tool calls in sequence (triggered by a single prompt) Parallel tool calls when appropriate Dynamic tool selection based on previous results Tool retry logic and error handling State persistence across tool calls For more information, see Tools. ​Defining tools Pass a list of tools to the agent. Tools can be specified as plain Python functions or coroutines.The tool decorator can be used to customize tool names, descriptions, argument schemas, and other properties. Copyfrom langchain.tools import tool from langchain.agents import create_agent @tool def search(query: str) -> str: """Search for information.""" return f"Results for: {query}" @tool def get_weather(location: str) -> str: """Get weather information for a location.""" return f"Weather in {location}: Sunny, 72°F" agent = create_agent(model, tools=[search, get_weather]) If an empty tool list is provided, the agent will consist of a single LLM node without tool-calling capabilities. ​Tool error handling To customize how tool errors are handled, use the @wrap_tool_call decorator to create middleware: Copyfrom langchain.agents import create_agent from langchain.agents.middleware import wrap_tool_call from langchain.messages import ToolMessage

[内容过长，已截断...]

---

*由智能学习助手自动生成*
