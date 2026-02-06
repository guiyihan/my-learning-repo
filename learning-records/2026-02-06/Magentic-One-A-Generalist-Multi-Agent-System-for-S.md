# Skip to main content Microsoft...

> 📅 记录时间: 2026/2/6 11:06:41
> 🔗 来源: [Magentic-One: A Generalist Multi-Agent System for Solving Complex Tasks - Microsoft Research](https://www.microsoft.com/en-us/research/articles/magentic-one-a-generalist-multi-agent-system-for-solving-complex-tasks/)

## AI总结

# Magentic-One 核心要点总结

## 系统概述
• **定位**：微软推出的通用多智能体系统，用于解决开放式网络和文件任务
• **发布时间**：2024年11月4日
• **开源**：基于Microsoft AutoGen框架的开源实现
• **目标**：从对话式AI向能够自主完成任务的代理式AI转变

## 架构设计
• **多智能体架构**：由一个主导智能体（Orchestrator）指挥四个专门智能体协同工作
• **Orchestrator（编排者）**：负责高层规划、任务分解、进度跟踪和错误恢复
• **双循环机制**：
  - 外循环：管理任务台账（事实、推测、计划）
  - 内循环：管理进度台账（当前进度、任务分配）

## 五大智能体角色
• **Orchestrator**：领导智能体，负责任务分解和整体协调
• **WebSurfer**：控制Chromium浏览器，执行网页导航、交互和信息读取
• **FileSurfer**：管理本地文件预览和目录导航
• **Coder**：专门编写代码、分析信息和创建新工件
• **ComputerTerminal**：提供控制台访问，执行程序和安装库

## 技术特点
• **模型无关性**：默认使用GPT-4o，但可集成不同的LLM和SLM
• **模块化设计**：类似面向对象编程，便于开发和重用
• **即插即用**：可轻松添加或移除智能体，无需重构整个系统
• **灵活配置**：不同智能体可使用不同模型（如Orchestrator使用OpenAI o1-preview）

## 性能评估
• **评估工具**：推出AutoGenBench开源评估工具，支持重复测试和隔离控制
• **测试基准**：在GAIA、AssistantBench和WebArena三个基准测试中评估
• **性能表现**：在GAIA和AssistantBench上达到与现有最佳方法相当的性能，在WebArena上具有竞争力
• **通用性**：无需修改核心能力或架构即可应对多种挑战性任务

## 风险与安全
• **识别的风险**：
  - 可能采取不可逆的操作（删除文件、发送邮件等）
  - 账户被暂停、尝试重置密码
  - 试图在社交媒体发帖或联系人类寻求帮助
• **安全措施**：
  - 进行红队测试评估有害内容、越狱和提示注入攻击
  - 建议在Docker沙箱容器中运行
  - 遵循最小权限和最大监督原则
  - 要求人类保持在环监控

## 安全建议
• **使用建议**：配合具有强对齐能力的模型使用
• **监控要求**：实施生成前后的过滤和密切的日志监控
• **人机协作**：系统应能评估操作的可逆性，在高风险操作前暂停并寻求人类输入
• **防御威胁**：需要防范网络钓鱼、社会工程和虚假信息等威胁

## 未来方向
• **研究重点**：安全性和负责任AI研究
• **社区协作**：邀请社区共同确保未来智能体系统既有用又安全
• **持续改进**：根据最新安全研究演进Magentic-One
• **开放挑战**：理解新兴风险并开发有效的缓解措施

## 资源获取
• **代码仓库**：GitHub开源
• **技术报告**：详细技术文档可查阅
• **开发框架**：基于AutoGen多智能体框架构建

## 原文摘录

Skip to main content
Microsoft
Research
Our research Programs & events Connect & learn About Register: Research Forum
All Microsoft
Search 

AI Frontiers
AI Frontiers
 AI Frontiers blog
Magentic-One: A Generalist Multi-Agent System for Solving Complex Tasks
Published November 4, 2024

Share this page

Share on Facebook
Share on X
Share on LinkedIn
Share on Reddit
Subscribe to our RSS feed
By Adam Fourney, Principal Researcher; Gagan Bansal, Senior Researcher; Hussein Mozannar, Senior Researcher; Victor Dibia, Principal Research Software Engineer; Saleema Amershi, Partner Research Manager

Contributors: Adam Fourney, Gagan Bansal, Hussein Mozannar, Cheng Tan, Eduardo Salinas, Erkang (Eric) Zhu, Friederike Niedtner, Grace Proebsting, Griffin Bassman, Jack Gerrits, Jacob Alber, Peter Chang, Ricky Loynd, Robert West, Victor Dibia, Ahmed Awadallah, Ece Kamar, Rafah Hosn, Saleema Amershi


We are introducing Magentic-One, our new generalist multi-agent system for solving open-ended web and file-based tasks across a variety of domains. Magentic-One represents a significant step towards developing agents that can complete tasks that people encounter in their work and personal lives. We are also releasing an open-source implementation of Magentic-One on Microsoft AutoGen, our popular open-source framework for developing multi-agent applications.
The future of AI is agentic. AI systems are evolving from having conversations to getting things done—this is where we expect much of AI’s value to shine. It’s the difference between generative AI recommending dinner options to agentic assistants that can autonomously place your order and arrange delivery. It’s the shift from summarizing research papers to actively searching for and organizing relevant studies in a comprehensive literature review.

Modern AI agents, capable of perceiving, reasoning, and acting on our behalf, are demonstrating remarkable performance in areas such as software engineering, data analysis, scientific research, and web navigation. Still, to fully realize the long-held vision of agentic systems that can enhance our productivity and transform our lives, we need advances in generalist agentic systems. These systems must reliably complete complex, multi-step tasks across a wide range of scenarios people encounter in their daily lives.

Introducing Magentic-One(opens in new tab), a high-performing generalist agentic system designed to solve such tasks. Magentic-One employs a multi-agent architecture where a lead agent, the Orchestrator, directs four other agents to solve tasks. The Orchestrator plans, tracks progress, and re-plans to recover from errors, while directing specialized agents to perform tasks like operating a web browser, navigating local files, or writing and executing Python code.

Magentic-One achieves statistically competitive performance to the state-of-the-art on multiple challenging agentic benchmarks, without requiring modifications to its core capabilities or architecture. Built on AutoGen(opens in new tab), our popular open-source multi-agent framework, Magentic-One’s modular, multi-agent design offers numerous advantages over monolithic single-agent systems. By encapsulating distinct skills in separate agents, it simplifies development and reuse, similar to object-oriented programming. Magentic-One’s plug-and-play design further supports easy adaptation and extensibility by enabling agents to be added or removed without needing to rework the entire system—unlike single-agent systems, which often struggle with inflexible workflows.

We’re making Magentic-One open-source(opens in new tab) for researchers and developers. While Magentic-One shows strong generalist capabilities, it’s still far from human-level performance and can make mistakes. Moreover, as agentic systems grow more powerful, their risks—like taking undesirable actions or enabling malicious use-cases—can also increase. While we’re still in the early days of modern agentic AI, we’re inviting the community to help tackle these open challenges and ensure our future agentic systems are both helpful and safe. To this end, we’re also releasing AutoGenBench(opens in new tab), an agentic evaluation tool with built-in controls for repetition and isolation to rigorously test agentic benchmarks and tasks while minimizing undesirable side-effects.

Code on GitHub
Read the technical report
How it works

Magentic-One features an Orchestrator agent that implements two loops: an outer loop and an inner loop. The outer loop (lighter background with solid arrows) manages the task ledger (containing facts, guesses, and plan) and the inner loop (darker background with dotted arrows) manages the progress ledger (containing current progress, task assignment to agents).
Magentic-One work is based on a multi-agent architecture where a lead Orchestrator agent is responsible for high-level planning, directing other agents and tracking task progress. The Orchestrator begins by creating a plan to tackle the task, gathering needed facts and educated guesses in a Task Ledger that is maintained. At each step of its plan, the Orchestrator creates a Progress Ledger where it self-reflects on task progress and checks whether the task is completed. If the task is not yet completed, it assigns one of Magentic-One other agents a subtask to complete. After the assigned agent completes its subtask, the Orchestrator updates the Progress Ledger and continues in this way until the task is complete. If the Orchestrator finds that progress is not being made for enough steps, it can update the Task Ledger and create a new plan. This is illustrated in the figure above; the Orchestrator work is thus divided into an outer loop where it updates the Task Ledger and an inner loop to update the Progress Ledger.

Magentic-One consists of the following agents:

Orchestrator: The lead agent responsible for task decomposition, planning, directing other agents in executing subtasks, tracking overall progress, and taking corrective actions as needed
WebSurfer: An LLM-based agent proficient in commanding and managing the state of a Chromium-based web browser. For each request, the WebSurfer performs actions such as navigation (e.g., visiting URLs, performing searches), interacting with webpages (e.g., clicking, typing), and reading actions (e.g., summarizing, answering questions). It then reports on the new state of the webpage. The WebSurfer relies on the browser’s accessibility tree and set-of-marks prompting to perform its tasks.
FileSurfer: An LLM-based agent that commands a markdown-based file preview application to read local files. It can also perform common navigation tasks such as listing directory contents and navigating through them.
Coder: An LLM-based agent specialized in writing code, analyzing information collected from the other agents, and creating new artifacts.
ComputerTerminal: Provides access to a console shell for executing programs and installing new libraries.
Together, Magentic-One’s agents equip the Orchestrator with the tools and capabilities it needs to solve a wide range of open-ended problems and autonomously adapt to, and act in, dynamic and ever-changing web and file-system environments.

While the default multimodal LLM used for all agents is GPT-4o, Magentic-One is model-agnostic, allowing the integration of heterogeneous models to support different capabilities or meet different cost requirements. For example, different LLMs and SLMs or specialized versions can power different agents. For the Orchestrator, we recommend a strong reasoning model, like GPT-4o. In a different configuration, we also experimented with using OpenAI o1-preview for the Orchestrator’s outer loop and for the Coder, while other agents continued to use GPT-4o.

Evaluation
To rigorously evaluate Magentic-One’s performance, we introduce AutoGenBench, an open-source standalone tool for running agentic benchmarks that allows repetition and isolation, e.g., to control for variance of stochastic LLM calls and side-effects of agents taking actions in the world. AutoGenBench facilitates agentic evaluation and allows adding new benchmarks. Using AutoGenBench, we can evaluate Magentic-One on a variety of benchmarks. Our criterion for selecting benchmarks is that they should involve complex multi-step tasks, with at least some steps requiring planning and tool use, including using web browsers to act on real or simulated webpages. We consider three benchmarks in this work that satisfy this criterion: GAIA, AssistantBench, and WebArena.

In the Figure below we show the performance of Magentic-One on the three benchmarks and compare with GPT-4 operating on its own and the per-benchmark highest-performing open-source baseline and non open-source benchmark specific baseline according to the public leaderboards as of October 21, 2024. Magentic-One (GPT-4o, o1) achieves statistically comparable performance to previous SOTA methods on both GAIA and AssistantBench and competitive performance on WebArena. Note that GAIA and AssistantBench have a hidden test set while WebArena does not, and thus WebArena results are self-reported. Together, these results establish Magentic-One as a strong generalist agentic system for completing complex tasks.


Evaluation results of Magentic-One on the GAIA, AssistantBench and WebArena. Error bars indicate 95% confidence intervals. Note that WebArena results are self-reported.
Risks and mitigations
Agentic systems like Magentic-One mark a significant shift in both the opportunities and risks associated with AI. Magentic-One interacts with a digital world designed for humans, taking actions that can change states and potentially lead to irreversible consequences. These inherent and undeniable risks were evident during our testing, where several emerging issues surfaced. For example, during development, a misconfiguration led agents to repeatedly attempt and fail to log into a WebArena website. This resulted in the account being temporarily suspended. The agents then tried to reset the account’s password. Even more concerning were cases in which agents, until explicitly stopped, attempted to recruit human assistance by posting on social media, emailing textbook authors, or even drafting a freedom of information request to a government entity. In each case, the agents were unsuccessful due to a lack of the required tools or accounts, or because human observers intervened.

Aligned with the Microsoft AI principles and Responsible AI practices, we worked to identify, measure, and mitigate potential risks before deploying Magentic-One. Specifically, we conducted red-teaming exercises to assess risks related to harmful content, jailbreaks, and prompt injection attacks, finding no increased risk from our design. Additionally, we provide cautionary notices and guidance for using Magentic-One safely, including examples and appropriate default settings. Users are advised to keep humans in the loop for monitoring, and ensure that all code execution examples, evaluations, and benchmarking tools are run in sandboxed Docker containers to minimize risks.

Recommendations and looking forward
We recommend using Magentic-One with models that have strong alignment, pre- and post-generation filtering, and closely monitored logs during and after execution. In our own use, we follow the principles of least privilege and maximum oversight. Minimizing risks associated with agentic AI will require new ideas and extensive research, as much work is still needed to understand these emerging risks and develop effective mitigations. We are committed to sharing our learnings with the community and evolving Magentic-One in line with the latest safety research.

As we look ahead, there are valuable opportunities to improve agentic AI, particularly in safety and Responsible AI research. Agents acting on the public web may be vulnerable to phishing, social engineering, and misinformation threats, much like human users. To counter these risks, an important direction is to equip agents with the ability to assess the reversibility of their actions—distinguishing between those that are easily reversible, those that require effort, and those that are irreversible. Actions like deleting files, sending emails, or filing forms are often difficult or impossible to undo. Systems should therefore be designed to pause and seek human input before proceeding with such high-risk actions.

We invite the community to collaborate with us in ensuring that future agentic systems are both helpful and safe.

For further information, results and discussion, please see our technical report.(opens in new tab)


Azure AI Foundry Labs
Get a glimpse of potential future directions for AI, with these experimental technologies from Microsoft Research.

Explore Azure AI Foundry
Opens in a new tab
Continue reading

April 18, 2025
PEACE project unlocks AI applications in geology using GeoMap 

February 25, 2025
AutoGen v0.4: Reimagining the foundation of agentic AI for scale, extensibility, and robustness 

January 2, 2025
RD-Agent: An open-source solution for smarter R&D 
October 8, 2024
OmniParser for pure vision-based GUI agent 
See all blog posts
Research Areas
Artificial intelligence
Related Tools
Magentic-One
Related labs
AI Frontiers
Follow us:

Follow on X
Like on Facebook
Follow on LinkedIn
Subscribe on Youtube
Follow on Instagram
Subscribe to our RSS feed
Share this page:

Share on X
Share on Facebook
Share on LinkedIn
Share on Reddit
What's new
Surface Pro
Surface Laptop
Surface Laptop Studio 2
Copilot for organizations
Copilot for personal use
AI in Windows
Explore Microsoft products
Windows 11 apps
Microsoft Store
Account profile
Download Center
Microsoft Store support
Returns
Order tracking
Certified Refurbished
Microsoft Store Promise
Flexible Payments
Education
Microsoft in education
Devices for education
Microsoft Teams for Education
Microsoft 365 Education
How to buy for your school
Educator training and development
Deals for students and parents
AI for education
Business
Microsoft AI
Microsoft Security
Dynamics 365
Microsoft 365
Microsoft Power Platform
Microsoft Teams
Microsoft 365 Copilot
Small Business
Developer & IT
Azure
Microsoft Developer
Microsoft Learn
Support for AI marketplace apps
Microsoft Tech Community
Microsoft Marketplace
Marketplace Rewards
Visual Studio
Company
Careers
About Microsoft
Company news
Privacy at Microsoft
Investors
Diversity and inclusion
Accessibility
Sustainability
Your Privacy Choices
Consumer Health Privacy
Sitemap Contact Microsoft Privacy Terms of use Trademarks Safety & eco Recycling About our ads © Microsoft 2026
📝 AI总结结果

📋 要点提炼
📄 一段概述
🗺️ 思维导图
简洁
标准
详细
选择总结类型和详细程度，然后点击下方按钮生成总结
保存到GitHub

---

*由智能学习助手自动生成*
