# Skip to main content Docs by L...

> 📅 记录时间: 2026/2/6 10:51:09
> 🔗 来源: [LangChain overview - Docs by LangChain](https://docs.langchain.com/oss/python/langchain/overview)

## 原文摘录

Skip to main content
Docs by LangChain home page

Open source

Search...
⌘K
Ask AI
GitHub
Try LangSmith

LangChain
LangGraph
Deep Agents
Integrations
Learn
Reference
Contribute

Python

Overview
Get started
Install
Quickstart
Changelog
Philosophy
Core components
Agents
Models
Messages
Tools
Short-term memory

Streaming
Structured output
Middleware
Overview
Built-in middleware
Custom middleware
Advanced usage
Guardrails
Runtime
Context engineering
Model Context Protocol (MCP)
Human-in-the-loop

Multi-agent
Retrieval
Long-term memory
Agent development
LangSmith Studio
Test
Agent Chat UI
Deploy with LangSmith
Deployment
Observability

On this page
Requirements
Build a basic agent
Build a real-world agent
Get started
Quickstart

Copy page

This quickstart takes you from a simple setup to a fully functional AI agent in just a few minutes.
LangChain Docs MCP server
If you’re using an AI coding assistant or IDE (e.g. Claude Code or Cursor), you should install the LangChain Docs MCP server to get the most out of it. This ensures your agent has access to up-to-date LangChain documentation and examples.
​
Requirements
For these examples, you will need to:
Install the LangChain package
Set up a Claude (Anthropic) account and get an API key
Set the ANTHROPIC_API_KEY environment variable in your terminal
Although these examples use Claude, you can use any supported model by changing the model name in the code and setting up the appropriate API key.
​
Build a basic agent
Start by creating a simple agent that can answer questions and call tools. The agent will use Claude Sonnet 4.5 as its language model, a basic weather function as a tool, and a simple prompt to guide its behavior.
from langchain.agents import create_agent

def get_weather(city: str) -> str:
    """Get weather for a given city."""
    return f"It's always sunny in {city}!"

agent = create_agent(
    model="claude-sonnet-4-5-20250929",
    tools=[get_weather],
    system_prompt="You are a helpful assistant",
)

# Run the agent
agent.invoke(
    {"messages": [{"role": "user", "content": "what is the weather in sf"}]}
)
To learn how to trace your agent with LangSmith, see the LangSmith documentation.
​
Build a real-world agent
Next, build a practical weather forecasting agent that demonstrates key production concepts:
Detailed system prompts for better agent behavior
Create tools that integrate with external data
Model configuration for consistent responses
Structured output for predictable results
Conversational memory for chat-like interactions
Create and run the agent to test the fully functional agent
Let’s walk through each step:
1
Define the system prompt

The system prompt defines your agent’s role and behavior. Keep it specific and actionable:
SYSTEM_PROMPT = """You are an expert weather forecaster, who speaks in puns.

You have access to two tools:

- get_weather_for_location: use this to get the weather for a specific location
- get_user_location: use this to get the user's location

If a user asks you for the weather, make sure you know the location. If you can tell from the question that they mean wherever they are, use the get_user_location tool to find their location."""
2
Create tools

Tools let a model interact with external systems by calling functions you define. Tools can depend on runtime context and also interact with agent memory.
Notice below how the get_user_location tool uses runtime context:
from dataclasses import dataclass
from langchain.tools import tool, ToolRuntime

@tool
def get_weather_for_location(city: str) -> str:
    """Get weather for a given city."""
    return f"It's always sunny in {city}!"

@dataclass
class Context:
    """Custom runtime context schema."""
    user_id: str

@tool
def get_user_location(runtime: ToolRuntime[Context]) -> str:
    """Retrieve user information based on user ID."""
    user_id = runtime.context.user_id
    return "Florida" if user_id == "1" else "SF"
Tools should be well-documented: their name, description, and argument names become part of the model’s prompt. LangChain’s @tool decorator adds metadata and enables runtime injection with the ToolRuntime parameter.
3
Configure your model

Set up your language model with the right parameters for your use case:
from langchain.chat_models import init_chat_model

model = init_chat_model(
    "claude-sonnet-4-5-20250929",
    temperature=0.5,
    timeout=10,
    max_tokens=1000
)
Depending on the model and provider chosen, initialization parameters may vary; refer to their reference pages for details.
4
Define response format

Optionally, define a structured response format if you need the agent responses to match a specific schema.
from dataclasses import dataclass

# We use a dataclass here, but Pydantic models are also supported.
@dataclass
class ResponseFormat:
    """Response schema for the agent."""
    # A punny response (always required)
    punny_response: str
    # Any interesting information about the weather if available
    weather_conditions: str | None = None
5
Add memory

Add memory to your agent to maintain state across interactions. This allows the agent to remember previous conversations and context.
from langgraph.checkpoint.memory import InMemorySaver

checkpointer = InMemorySaver()
In production, use a persistent checkpointer that saves message history to a database. See Add and manage memory for more details.
6
Create and run the agent

Now assemble your agent with all the components and run it!
from langchain.agents.structured_output import ToolStrategy

agent = create_agent(
    model=model,
    system_prompt=SYSTEM_PROMPT,
    tools=[get_user_location, get_weather_for_location],
    context_schema=Context,
    response_format=ToolStrategy(ResponseFormat),
    checkpointer=checkpointer
)

# `thread_id` is a unique identifier for a given conversation.
config = {"configurable": {"thread_id": "1"}}

response = agent.invoke(
    {"messages": [{"role": "user", "content": "what is the weather outside?"}]},
    config=config,
    context=Context(user_id="1")
)

print(response['structured_response'])
# ResponseFormat(
#     punny_response="Florida is still having a 'sun-derful' day! The sunshine is playing 'ray-dio' hits all day long! I'd say it's the perfect weather for some 'solar-bration'! If you were hoping for rain, I'm afraid that idea is all 'washed up' - the forecast remains 'clear-ly' brilliant!",
#     weather_conditions="It's always sunny in Florida!"
# )


# Note that we can continue the conversation using the same `thread_id`.
response = agent.invoke(
    {"messages": [{"role": "user", "content": "thank you!"}]},
    config=config,
    context=Context(user_id="1")
)

print(response['structured_response'])
# ResponseFormat(
#     punny_response="You're 'thund-erfully' welcome! It's always a 'breeze' to help you stay 'current' with the weather. I'm just 'cloud'-ing around waiting to 'shower' you with more forecasts whenever you need them. Have a 'sun-sational' day in the Florida sunshine!",
#     weather_conditions=None
# )
Show Full example code

To learn how to trace your agent with LangSmith, see the LangSmith documentation.
Congratulations! You now have an AI agent that can:
Understand context and remember conversations
Use multiple tools intelligently
Provide structured responses in a consistent format
Handle user-specific information through context
Maintain conversation state across interactions
Edit this page on GitHub or file an issue.
Connect these docs to Claude, VSCode, and more via MCP for real-time answers.
Was this page helpful?


Yes

No

Docs by LangChain home page
Resources

Forum
Changelog
LangChain Academy
Trust Center
Company

Home
About
Careers
Blog
github
x
linkedin
youtube
Powered by

## AI总结

（快速保存，未生成总结）

---

*由智能学习助手自动生成*
