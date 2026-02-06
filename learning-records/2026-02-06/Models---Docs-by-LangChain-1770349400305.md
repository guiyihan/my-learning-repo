# Models - Docs by LangChain

> 📅 记录时间: 2026/2/6 11:43:20
> 🔗 来源: [Models - Docs by LangChain](https://docs.langchain.com/oss/python/langchain/models)

## AI总结

# 网页内容分析总结

## 1. 核心主题

这是 **LangChain 框架中模型（Models）组件的官方文档**，主要介绍如何在 LangChain 中使用和集成各种大语言模型（LLM），包括模型的初始化、调用方法、工具调用等核心功能。

---

## 2. 关键要点

### ⭐ **模型的核心能力**
- **文本生成**：内容创作、翻译、摘要、问答
- **工具调用**：调用外部工具（数据库查询、API调用）
- **结构化输出**：按预定义格式生成响应
- **多模态支持**：处理图像、音频、视频
- **推理能力**：多步骤推理得出结论

### ⭐ **两种使用场景**
- **与 Agent 配合使用**：模型作为 Agent 的推理引擎，驱动决策过程
- **独立使用**：直接调用模型完成文本生成、分类、提取等任务

### ⭐ **三种主要调用方法**
1. **Invoke**：同步调用，返回完整响应
2. **Stream**：流式输出，实时显示生成内容
3. **Batch**：批量处理多个请求，提高效率

### ⭐ **统一的模型接口**
- 支持所有主流提供商（OpenAI、Anthropic、Google、AWS Bedrock 等）
- 通过 `init_chat_model` 轻松初始化和切换不同模型
- 标准化的参数配置（temperature、max_tokens、timeout 等）

### ⭐ **工具调用（Tool Calling）**
- 通过 `bind_tools()` 绑定自定义工具
- 模型可以请求执行工具并使用结果
- 支持强制工具调用和并行工具调用

---

## 3. 重要细节

### 📌 **模型初始化示例**
```python
from langchain.chat_models import init_chat_model
model = init_chat_model("gpt-4.1")
response = model.invoke("Why do parrots talk?")
```

### 📌 **消息格式**
- 支持字典格式和消息对象格式
- 消息包含角色：system（系统）、user（用户）、assistant（助手）
- 可传递对话历史进行上下文理解

### 📌 **流式输出机制**
- `stream()` 返回迭代器，逐块生成内容
- 返回 `AIMessageChunk` 对象，可通过相加聚合为完整消息
- 支持自动流式传输（auto-streaming）

### 📌 **批处理优化**
- `batch()` 并行处理多个请求
- `batch_as_completed()` 可按完成顺序返回结果
- 通过 `max_concurrency` 控制并发数

### 📌 **工具调用流程**
1. 使用 `@tool` 装饰器定义工具
2. 通过 `bind_tools()` 绑定到模型
3. 模型返回 `tool_calls` 请求
4. 执行工具并返回结果
5. 将结果传回模型生成最终响应

### 📌 **注意事项**
- 确保使用聊天模型（Chat 前缀），而非旧版 LLM（返回字符串）
- 流式传输需要整个程序栈都支持流式处理
- 工具执行在非 Agent 场景需手动处理

---

## 4. 个人学习价值

### 💡 **实用性价值**
- **快速上手**：提供了清晰的初始化和调用示例，可直接应用于项目
- **灵活性**：统一接口支持多提供商切换，降低供应商锁定风险
- **性能优化**：学习到批处理和并发控制等性能优化技巧

### 💡 **架构设计启发**
- **抽象层设计**：LangChain 通过统一接口屏蔽不同提供商的差异
- **流式处理思维**：理解流式输出在用户体验中的重要性
- **工具编排模式**：学习如何设计可扩展的工具调用系统

### 💡 **开发实践指导**
- 了解如何根据任务选择合适的模型（指令遵循、结构化推理、上下文窗口）
- 掌握对话历史管理和上下文传递技巧
- 理解 Agent 与独立调用的区别和适用场景

### 💡 **进阶方向**
- 深入研究 `astream_events()` 用于复杂事件流处理
- 探索强制工具调用和并行工具调用的高级用法
- 学习如何实现自定义工具执行循环

---

**总结**：这是一份非常实用的技术文档，既适合初学者快速入门 LangChain 模型调用，也为有经验的开发者提供了性能优化和高级功能的参考。建议结合实际项目边学边练，重点掌握三种调用方法和工具调用机制。

## 原文摘录

Skip to main contentDocs by LangChain home pageOpen sourceSearch...NavigationCore componentsModelsCore componentsModelsCopy pageLLMs are powerful AI tools that can interpret and generate text like humans. They’re versatile enough to write content, translate languages, summarize, and answer questions without needing specialized training for each task. In addition to text generation, many models support: Tool calling - calling external tools (like databases queries or API calls) and use results in their responses. Structured output - where the model’s response is constrained to follow a defined format. Multimodality - process and return data other than text, such as images, audio, and video. Reasoning - models perform multi-step reasoning to arrive at a conclusion. Models are the reasoning engine of agents. They drive the agent’s decision-making process, determining which tools to call, how to interpret results, and when to provide a final answer. The quality and capabilities of the model you choose directly impact your agent’s baseline reliability and performance. Different models excel at different tasks - some are better at following complex instructions, others at structured reasoning, and some support larger context windows for handling more information. LangChain’s standard model interfaces give you access to many different provider integrations, which makes it easy to experiment with and switch between models to find the best fit for your use case. For provider-specific integration information and capabilities, see the provider’s chat model page. ​Basic usage Models can be utilized in two ways: With agents - Models can be dynamically specified when creating an agent. Standalone - Models can be called directly (outside of the agent loop) for tasks like text generation, classification, or extraction without the need for an agent framework. The same model interface works in both contexts, which gives you the flexibility to start simple and scale up to more complex agent-based workflows as needed. ​Initialize a model The easiest way to get started with a standalone model in LangChain is to use init_chat_model to initialize one from a chat model provider of your choice (examples below): OpenAI Anthropic Azure Google Gemini AWS Bedrock HuggingFace👉 Read the OpenAI chat model integration docsCopypip install -U "langchain[openai]" init_chat_modelModel ClassCopyimport os from langchain.chat_models import init_chat_model os.environ["OPENAI_API_KEY"] = "sk-..." model = init_chat_model("gpt-4.1") Copyresponse = model.invoke("Why do parrots talk?") See init_chat_model for more detail, including information on how to pass model parameters. ​Supported models LangChain supports all major model providers, including OpenAI, Anthropic, Google, Azure, AWS Bedrock, and more. Each provider offers a variety of models with different capabilities. For a full list of supported models in LangChain, see the integrations page. ​Key methods InvokeThe model takes messages as input and outputs messages after generating a complete response. StreamInvoke the model, but stream the output as it is generated in real-time. BatchSend multiple requests to a model in a batch for more efficient processing. In addition to chat models, LangChain provides support for other adjacent technologies, such as embedding models and vector stores. See the integrations page for details. ​Parameters A chat model takes parameters that can be used to configure its behavior. The full set of supported parameters varies by model and provider, but standard ones include: ​modelstringrequiredThe name or identifier of the specific model you want to use with a provider. You can also specify both the model and its provider in a single argument using the ’:’ format, for example, ‘openai:o1’. ​api_keystringThe key required for authenticating with the model’s provider. This is usually issued when you sign up for access to the model. Often accessed by setting an environment variable. ​temperaturenumberControls the randomness of the model’s output. A higher number makes responses more creative; lower ones make them more deterministic. ​max_tokensnumberLimits the total number of tokens in the response, effectively controlling how long the output can be. ​timeoutnumberThe maximum time (in seconds) to wait for a response from the model before canceling the request. ​max_retriesnumberThe maximum number of attempts the system will make to resend a request if it fails due to issues like network timeouts or rate limits. Using init_chat_model, pass these parameters as inline **kwargs: Initialize using model parametersCopymodel = init_chat_model( "claude-sonnet-4-5-20250929", # Kwargs passed to the model: temperature=0.7, timeout=30, max_tokens=1000, ) Each chat model integration may have additional params used to control provider-specific functionality.For example, ChatOpenAI has use_responses_api to dictate whether to use the OpenAI Responses or Completions API.To find all the p

[内容过长，已截断...]

---

*由智能学习助手自动生成*
