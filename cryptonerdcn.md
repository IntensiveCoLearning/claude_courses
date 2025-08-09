---
timezone: UTC+9
---

# cryptonerdcn

**GitHub ID:** cryptonerdcn

**Telegram:** @cryptonerdcn

## Self-introduction

cryptonerdcn

## Notes

<!-- Content_START -->
# 2025-08-09

同步发布于推特：https://x.com/cryptonerdcn/status/1954128988303188062
第十四天打卡：
Sampling将MCP server的复杂性解绑，遇上它单独没法完成的任务（比如在llm提出需求后需要精加工而不是简单的访问某个api就能完成的任务），可以让client呼叫llm帮他完成一部分内容。
这样避免了MCP Server自己去与LLM通信。
MCP server需要用 create_message 函数发出让client去帮忙与LLM通信的请求。
Client同样需要一个sampling_callback来处理这个请求。

更大的好处是，LLM的token使用现在放到了Client中，这样公开的MCP Server就不需要内包直接调用LLM的能力（以及为此付费的代价），任何接入该MCP Server的Clinet都可以直接提供自己的LLM即可。
当然，Client需要小心被攻击。

# 2025-08-08

MCP前半部分完成。
证书：http://verify.skilljar.com/c/qc6f3w7xndi9

# 2025-08-06

同步发布于推特： https://x.com/cryptonerdcn/status/1953044806277693453

第十三天打卡：
有人问我Custom Commands和Hooks有啥不同？
实际上仅论脚本功能而言，执行起来了那并没什么不同，同一份脚本你可以用作Custom Commands也可以用作Hooks。区别在于他们一个是被动执行，另一个则是自动执行，所以适用的范围不同。
这是个完全看个人风格和项目目标的事情，有些人可能会把一个Custom Commands里什么都写好了，完全不需要Hooks插手。

# 2025-08-05

同步发于推特：https://x.com/cryptonerdcn/status/1952732409998483880
第十二天打卡：
之前在读MCP文档时就发现Claude Code的一些功能很明显是根据MCP的不同feature来定义的，现在看见课程里针对Resource这个feature使用了@ 文件读取内容来说明就更觉得实在是太像了（虽然内部逻辑可能是单纯的function calling而不是mcp）。
Resource对应的Claude Code的@ 操作
Prompt对应了Claude Code的Custom Command
Sampling对应的则是Claude Code的Hooks

# 2025-08-04

同步发布于推特：https://x.com/cryptonerdcn/status/1952316438217379858
第十一天打卡：
不少人应该已经知道的这条--虽然MCP Server有Prompts, Resources, Tools三个feature，但以Geimini为首的一些大模型只支持Tools。而实际上，Resources和Prompts也的确完全也可以用Tools的方式来编写。
个人推测这原因是因为LLM调用Function Calling的能力提升以及周边配套SDK把一些基础工作都做了之后，这套SDK可以通过很少的变化就能调用MCP的Tools。

# 2025-08-03

同步发于推特：https://x.com/cryptonerdcn/status/1951995712285069805

第十天打卡：

昨天休息一天，今天开始回头复习一下MCP的课程。
小tips:
如果你用的python开发MCP server，除了官方SDK外，还可以使用FastMCP。昨天还写了篇实际经验贴，见（https://x.com/cryptonerdcn/status/1951641736725532982）。
另有一个12万浏览的MCP经验贴见此（https://x.com/cryptonerdcn/status/1945045817338826862）

# 2025-08-01

同步发布于推特：
https://x.com/cryptonerdcn/status/1951287786436436441

第九天打卡：

Claude Code SDK还真是没用过，不过的确跟官方说的一样，完全可以嵌入开发的各个流程中，就是token消耗是真的打不住了。

The Claude Code SDK shines when integrated into larger development workflows. Consider using it for:

- Git hooks that automatically review code changes
- Build scripts that analyze and optimize code
- Helper commands for code maintenance tasks
- Automated documentation generation
- Code quality checks in CI/CD pipelines

# 2025-07-31

同步发于推特：
https://x.com/cryptonerdcn/status/1950914587987157376

第八天打卡：
Hooks tips:
先让Claude code把tools列出来，好知道我们要在哪个tools执行前后加hooks。
而Hooks除了是自动触发之外，如果不涉及权限的内容，仅仅只是执行逻辑的话，和Custom Command区别不大。
由于我的编程style，这的确造成了我更多的喜欢自己去检查和触发一些逻辑而不是完全交给自动化流程。

# 2025-07-30

同步发布于推特：
https://x.com/cryptonerdcn/status/1950559530254999705
第七天打卡 :
Hooks是接触Claude Code第一天就知道的功能，但直到今天，一共用CC写了7，8个项目了，却一直没用过。
个人感觉原因可能还是我现在流程不够自动化，手动介入的比较多。
不过我暂时没觉得这个有什么不好的，估计是因为还能Cover的住AI的能力，AI写出来的代码还是能让我看出明显的问题，所以才需要经常手动介入。

# 2025-07-29

同步发布于推特：
https://x.com/cryptonerdcn/status/1950186027110248452

第六天打卡：
Custom commands，这其实正好契合了凌晨我们讨论的workflow和agent的内容。
Claude code的Custom commands就是典型定义好的workflow，但在执行过程中，有些步骤（或者所有步骤）是由agent执行的。
Claude Code本体又可以算一个agent，它来调用这个workflow,表现出了一种你中有我我中有你的模糊境界。

https://x.com/cryptonerdcn/status/1949879799054516415

# 2025-07-28

同步发布于推特：https://x.com/cryptonerdcn/status/1949846486969352526
第五天打卡：
个人备忘，之前没把这块仔细看。
Plan mode:
pressing Shift + Tab twice (or once if you're already auto-accepting edits)

Thinking Modes
Claude offers different levels of reasoning through "thinking" modes. These allow Claude to spend more time reasoning about complex problems before providing solutions.

The available thinking modes include:

"Think" - Basic reasoning
"Think more" - Extended reasoning
"Think a lot" - Comprehensive reasoning
"Think longer" - Extended time reasoning
"Ultrathink" - Maximum reasoning capability

还真的没怎么用过Thinking mode, 这周准备试一试。

# 2025-07-27

同步发布于 https://x.com/cryptonerdcn/status/1949481983672983598

第四天打卡：

我发现Claude Code新用户经常产生误解的一个问题：别人分享的Claude.md拿过来就能用。

实际上无论是新开项目还是既有项目，官方都是推荐使用/init 来生成Claude.md，因为这里面还包含了该项目的各种信息而不仅仅是规则。
我个人做法是将别人分享的，或者准备复用现有的Claude.md 改名成类似于 Claude_template.md后，再执行/init命令。

新开项目空空如也，怎么用/init生成有意义的 Claude.md呢？也很简单，在readme.md 里尽可能详细的写上项目的信息，如需求和目的，所使用技术栈，不同开发阶段的不同功能等，Claude code会自己分析的。

# 2025-07-26

同步发于推特：https://x.com/cryptonerdcn/status/1949058784552440285

尝试了下怎么在Claude code中使用 playwright， 以前都是在cursor里使用。
playwright本身的使用方法不再强调，只说在Claude中需要添加的部分。

最简单的添加：
`claude mcp add playwright npx @playwright/mcp@latest`

个人现在使用下面的rules(放入Claude.md):

## Playwright MCP Usage Rules
### Absolute Prohibitions
- Execution of any code in any form is strictly prohibited
- No code execution to investigate MCP tools
- No approaches involving subprocess or command execution

### Only direct invocation of MCP tools is allowed
- playwright:browser_navigate
- playwright:browser_screenshot
- Other Playwright MCP tools


之后修改UI时提出要求，然后让Claude自行截图和修正即可。

# 2025-07-25

同步发于推特：https://x.com/cryptonerdcn/status/1948669514562122049
看到官方教程的安排和我之前在社内分享时的思路几乎一模一样，愈加觉得我在有限的时间里，还抽出一大段时间来讲LLM只有上下文处理能力，以及Function Call到MCP的演变历程这个安排是合理的。
LLM本身的实现也许可以看作是力大砖飞的魔法，但围绕 LLM 构建起来的工具链、产品和功能，大多归根结底还是上下文工程的延伸，更多的是属于传统开发的领域。
对于并非LLM相关研究者的普通工程师（以及非工程师）来说，有机会参与的真正的挑战与创新，并不在模型本体，而是应该思考，如何以工程化的方式组织如Prompt、Function Call、RAG等模块，使得LLM这一上下文处理引擎能够在特定场景下稳定地产生智能行为--而且还要趁早，不然你想到的创新点可能很快就被做进LLM提供商的SDK，甚至直接被LLM接管。

# 2025-07-24

同步发于TG群组。
个人心得：
1. 如果想要使用Claude code，请直接访问 https://anthropic.skilljar.com/claude-code-in-action
2. https://anthropic.skilljar.com/claude-with-the-anthropic-api 课程对于已经学过openai和吴恩达的课程的同学没什么新东西，学过的可以直接跳到claude自家工具和mcp概念那部分。

3. Claude with Amazon Bedrock和 Claude with Google Cloud's Vertex AI 你不用相关平台的东西就不用学。


个人重点看两个MCP相关的了。


<!-- Content_END -->
