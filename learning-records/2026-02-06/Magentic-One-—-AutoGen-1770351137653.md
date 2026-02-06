# Magentic-One — AutoGen

> 📅 记录时间: 2026/2/6 12:12:17
> 🔗 来源: [Magentic-One — AutoGen](https://microsoft.github.io/autogen/stable/user-guide/agentchat-user-guide/magentic-one.html)

## AI总结

# Magentic-One 网页内容分析总结

## 1. 核心主题

**Magentic-One** 是微软开发的一个通用多智能体系统，用于解决开放式的网络和文件任务。它是AutoGen框架的重要组成部分，代表了多智能体系统的重大进步，现已从autogen-core移植到autogen-agentchat，提供更模块化和易用的接口。

## 2. 关键要点

### ⭐ **多智能体协作架构**
- 采用**指挥官-执行者**模式，由Orchestrator（编排者）作为核心领导智能体，负责高层规划、任务分解和进度追踪
- 配备4个专业化智能体：WebSurfer（网页浏览）、FileSurfer（文件处理）、Coder（代码编写）、ComputerTerminal（代码执行）

### ⭐ **双循环工作机制**
- **外循环**：更新任务账本(Task Ledger)，调整整体计划
- **内循环**：更新进度账本(Progress Ledger)，监控子任务完成情况
- 当进度停滞时自动重新规划

### ⭐ **模型无关性与灵活配置**
- 默认使用GPT-4o，但支持异构模型组合
- 可为不同智能体配置不同LLM/SLM以平衡性能和成本
- 推荐为Orchestrator使用强推理模型

### ⭐ **安全性警示**
- 必须在Docker容器和虚拟环境中运行
- 需要人工监督和审批机制（特别是代码执行）
- 可能遭受网页提示注入攻击
- 智能体可能尝试风险行为（如招募人类帮助、自动接受cookie协议）

### ⭐ **易用性提升**
- 提供三种使用方式：
  1. 替换标准的SelectorGroupChat为MagenticOneGroupChat
  2. 在自定义团队中使用单个Magentic-One智能体
  3. 使用MagenticOne助手类获得完整配置

## 3. 重要细节

### 📌 **技术实现细节**
- **WebSurfer** 使用可访问性树(accessibility tree)和set-of-marks提示技术操控浏览器
- **FileSurfer** 基于markdown预览应用读取多种文件类型
- 支持异步编程(asyncio)和流式输出

### 📌 **安装要求**
```bash
pip install "autogen-agentchat" "autogen-ext[magentic-one,openai]"
playwright install --with-deps chromium  # 使用WebSurfer时需要
```

### 📌 **性能表现**
- 在多个智能体基准测试中表现出竞争力
- 成功完成GAIA基准测试中的复杂任务
- 2024年11月首次发布

### 📌 **批准机制示例**
提供了代码执行前的人工审批函数模板，这是安全运行的关键实践

## 4. 个人学习价值

### 💡 **架构设计启发**
- **分层协作模式**：学习如何设计"管理者-执行者"的智能体架构，这种模式可应用于复杂系统设计
- **自我反思机制**：Task Ledger和Progress Ledger的双账本设计展示了系统如何进行自我监控和调整

### 💡 **实践指导意义**
- 提供了完整的从简单到复杂的代码示例，适合渐进式学习
- 强调安全实践，教会如何在AI自主性和安全控制之间平衡
- 展示了模块化设计的重要性（从core到agentchat的演进）

### 💡 **前沿技术洞察**
- 了解多模态LLM在实际任务中的应用方式
- 认识到通用智能体系统的可能性和局限性
- 理解提示注入等新兴安全威胁

### 💡 **工程化思维**
- 学习如何将研究原型转化为可用工具（提供helper类简化使用）
- 理解开源项目如何平衡灵活性和易用性
- 掌握异步编程在AI系统中的应用模式

**总结**：这是一个兼具学术价值和工程实践意义的多智能体系统，适合对AI Agent、自动化任务处理和复杂系统设计感兴趣的学习者深入研究。

## 原文摘录

Magentic-One# Magentic-One is a generalist multi-agent system for solving open-ended web and file-based tasks across a variety of domains. It represents a significant step forward for multi-agent systems, achieving competitive performance on a number of agentic benchmarks (see the technical report for full details). When originally released in November 2024 Magentic-One was implemented directly on the autogen-core library. We have now ported Magentic-One to use autogen-agentchat, providing a more modular and easier to use interface. To this end, the Magentic-One orchestrator MagenticOneGroupChat is now simply an AgentChat team, supporting all standard AgentChat agents and features. Likewise, Magentic-One’s MultimodalWebSurfer, FileSurfer, and MagenticOneCoderAgent agents are now broadly available as AgentChat agents, to be used in any AgentChat workflows. Lastly, there is a helper class, MagenticOne, which bundles all of this together as it was in the paper with minimal configuration. Find additional information about Magentic-one in our blog post and technical report. Example: The figure above illustrates Magentic-One multi-agent team completing a complex task from the GAIA benchmark. Magentic-One’s Orchestrator agent creates a plan, delegates tasks to other agents, and tracks progress towards the goal, dynamically revising the plan as needed. The Orchestrator can delegate tasks to a FileSurfer agent to read and handle files, a WebSurfer agent to operate a web browser, or a Coder or Computer Terminal agent to write or execute code, respectively. Caution Using Magentic-One involves interacting with a digital world designed for humans, which carries inherent risks. To minimize these risks, consider the following precautions: Use Containers: Run all tasks in docker containers to isolate the agents and prevent direct system attacks. Virtual Environment: Use a virtual environment to run the agents and prevent them from accessing sensitive data. Monitor Logs: Closely monitor logs during and after execution to detect and mitigate risky behavior. Human Oversight: Run the examples with a human in the loop to supervise the agents and prevent unintended consequences. Limit Access: Restrict the agents’ access to the internet and other resources to prevent unauthorized actions. Safeguard Data: Ensure that the agents do not have access to sensitive data or resources that could be compromised. Do not share sensitive information with the agents. Be aware that agents may occasionally attempt risky actions, such as recruiting humans for help or accepting cookie agreements without human involvement. Always ensure agents are monitored and operate within a controlled environment to prevent unintended consequences. Moreover, be cautious that Magentic-One may be susceptible to prompt injection attacks from webpages. Getting started# Install the required packages: pip install "autogen-agentchat" "autogen-ext[magentic-one,openai]" # If using the MultimodalWebSurfer, you also need to install playwright dependencies: playwright install --with-deps chromium Copy to clipboard If you haven’t done so already, go through the AgentChat tutorial to learn about the concepts of AgentChat. Then, you can try swapping out a autogen_agentchat.teams.SelectorGroupChat with MagenticOneGroupChat. For example: import asyncio from autogen_ext.models.openai import OpenAIChatCompletionClient from autogen_agentchat.agents import AssistantAgent from autogen_agentchat.teams import MagenticOneGroupChat from autogen_agentchat.ui import Console async def main() -> None: model_client = OpenAIChatCompletionClient(model="gpt-4o") assistant = AssistantAgent( "Assistant", model_client=model_client, ) team = MagenticOneGroupChat([assistant], model_client=model_client) await Console(team.run_stream(task="Provide a different proof for Fermat's Last Theorem")) await model_client.close() asyncio.run(main()) Copy to clipboard To use a different model, see Models for more information. Or, use the Magentic-One agents in a team: Caution The example code may download files from the internet, execute code, and interact with web pages. Ensure you are in a safe environment before running the example code. import asyncio from autogen_ext.models.openai import OpenAIChatCompletionClient from autogen_agentchat.teams import MagenticOneGroupChat from autogen_agentchat.ui import Console from autogen_ext.agents.web_surfer import MultimodalWebSurfer # from autogen_ext.agents.file_surfer import FileSurfer # from autogen_ext.agents.magentic_one import MagenticOneCoderAgent # from autogen_agentchat.agents import CodeExecutorAgent # from autogen_ext.code_executors.local import LocalCommandLineCodeExecutor async def main() -> None: model_client = OpenAIChatCompletionClient(model="gpt-4o") surfer = MultimodalWebSurfer( "WebSurfer", model_client=model_client, ) team = MagenticOneGroupChat([surfer], model_client=model_client) await Console(team.run_stream(task="What is the UV index in Melbourn

[内容过长，已截断...]

---

*由智能学习助手自动生成*
