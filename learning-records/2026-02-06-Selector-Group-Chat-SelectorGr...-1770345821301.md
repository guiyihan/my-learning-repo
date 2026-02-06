# Selector Group Chat SelectorGr...

> 📅 记录时间: 2026/2/6 10:43:41
> 🔗 来源: [Selector Group Chat — AutoGen](https://microsoft.github.io/autogen/stable/user-guide/agentchat-user-guide/selector-group-chat.html#how-does-it-work)

## 原文摘录

Selector Group Chat
SelectorGroupChat implements a team where participants take turns broadcasting messages to all other members. A generative model (e.g., an LLM) selects the next speaker based on the shared context, enabling dynamic, context-aware collaboration.

Key features include:

Model-based speaker selection

Configurable participant roles and descriptions

Prevention of consecutive turns by the same speaker (optional)

Customizable selection prompting

Customizable selection function to override the default model-based selection

Customizable candidate function to narrow-down the set of agents for selection using model

## AI总结

# SelectorGroupChat 功能要点

• **核心机制**：实现团队成员轮流向所有其他成员广播消息的协作模式

• **智能选择**：使用生成模型（如大语言模型）根据共享上下文选择下一位发言者

• **动态协作**：支持基于上下文感知的动态协作方式

• **角色配置**：可配置参与者的角色和描述信息

• **防重复发言**：可选择性地防止同一发言者连续发言

• **自定义提示**：支持自定义选择发言者时的提示内容

• **自定义选择函数**：允许覆盖默认的基于模型的选择机制，实现自定义选择逻辑

• **候选者筛选**：支持自定义候选函数，在使用模型选择前缩小候选代理的范围

---

*由智能学习助手自动生成*
