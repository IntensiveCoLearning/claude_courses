---
timezone: UTC+12
---

# Muxin

**GitHub ID:** muxin-web3

**Telegram:** @muxin_eth

## Self-introduction

我是一名 Engineer + Web3 Buidler，最近在学习和 AI 相关的知识和工具，主要是为了帮助自己提高开发产品的效率，希望能坚持完成本次残酷共学！

## Notes

<!-- Content_START -->

# 2025-08-22
<!-- DAILY_CHECKIN_2025-08-22_START -->
最后一天啦！恭喜坚持到最后的自己，今天主要是完成了最后的 final assessment，获得了证书，开心！虽然这一期残酷共学结束了，但是对我来说，只是一个开始，接下来就要开始运用起来并学习其他还没接触到的知识。
test123
<!-- DAILY_CHECKIN_2025-08-22_END -->

# 2025-08-21

今天学习了最后一点内容，Agents
- Agents 代表了从结构化 Workflows 向灵活问题解决的转变, Workflows 适用于明确步骤的任务，Agents 适用于不确定解决路径的任务, 给 Agents 一个目标和工具集，让它自己找出实现目标的方法。对于 Agents，设计 Tools 的原则是抽象化成通用的 Tool，而不是高度专业化的 Tool，比如 bash, read, write, edit, glob, grep 都是比较好的 Claude Code Tool 设计的例子。
- Environment inspection，Claude 是"盲目"操作的，需要观察和理解行为结果才能有效工作，就像 Computer Use 功能中，每次操作后都要截图查看结果。核心原则是先读后写，修改文件前必须先理解当前内容，这样才能安全地进行修改而不破坏现有功能。
  - 好处：更好的进度跟踪，错误处理能力，质量保证，自适应行为调整。
- Agents vs Workflows
  - 特点：
    - Agents：Claude 获得基础工具集，自主制定计划；适用于未知任务类型；能创造性地组合工具解决各种挑战
    - Workflows：预定义的 Claude 调用序列；适用于已知问题和步骤；将大任务分解为小的、具体的子任务
  - 优势：
    - Agents：用户体验更灵活；任务完成灵活性强；能处理开发时未预料的新情况；需要时可向用户询问额外输入
    - Workflows：Claude 可专注单一子任务，准确性更高；更容易评估和测试；执行更可预测和可靠；更适合解决特定的明确定义问题
  - 缺点：
    - Agents：任务完成成功率较低；更难进行监测、测试和评估；行为可预测性较差
    - Workflows：灵活性差，只能解决特定类型任务；用户体验受限；需要更多前期规划设计

# 2025-08-20

今天学习了Agents and workflows，主要学习了几种不同的 workflow。
- Agent 和 workflow 的区别简单来说就是，如果你明确知道要解决的问题以及解决这个问题的步骤，选择 workflow，如果你不确定如何给 Claude 下达具体的任务，但你只知道一个 goal，使用 Agent。
- Evaluator-Optimizer 是一种 workflow pattern，Input -> Producer <-> Grader -> Output，在 Producer 和 Grader 之间是一个 submission 和 feedback 循环的过程，知道结果满意，才会输出给用户。
- Parallelization workflow，将单一的 task 拆分成多个 sub-tasks，然后并行运行 sub-tasks，最终把结果合并输出。
- Chaining workflow，把一个大的 task 拆分成几个小的非并行的 subtask，然后顺序执行，这样 Claude 每次只 focus 在一个任务上。
- Routing workflow，先将用户的 query/task 进行分类，然后根据分类将 task 路由到专门的处理 pipeline，而不需要使用通用的 prompt。

时间过得好快啊，只剩三天了！

# 2025-08-19

今天学了一下 Computer Use, 它是 Claude 的一个强大功能，让 AI 能够像人类一样直接与桌面环境交互和控制计算机。
- 主要应用场景：
  - 自动化 QA 测试 Web 应用程序
  - 数据录入和表单填写
  - 网站导航和信息收集
  - UI 测试和验证
  - 重复性桌面任务
- Computer Use 本质上就是 Tool Use 的特殊实现，遵循相同的底层系统架构。
- 工作流程对比：
  - 常规工具使用：发送消息 -> 工具模式 -> 执行函数 -> 返回结果
  - Computer Use：发送消息 -> 计算机工具 -> 执行桌面操作 -> 返回截图等结果

# 2025-08-18

今天学习了 Claude Code，因为已经用了一段时间了，所以比较熟悉，但目前还没添加过 MCP，之后试一下。
- Claude Code 有哪些 tool 可以帮助到你的 development workflow：
  - File operations - Search, read, and edit files in your project
  - Terminal access - Run commands directly from the conversation
  - Web access - Search documentation, fetch code examples, and more
  - MCP Server support - Add additional tools by connecting MCP servers
- Claude Code 可以贯穿整个 project 的生命周期：
  - Discover
  - Design
  - Build
  - Deploy
  - Support & Scale
- /init 命令会让 Claude Code 了解你整个项目的内容并生成一个 CLAUDE.md 的文件来 summarize 整个项目的情况，也相当于一个 project context。
- Common workflow：
  - Step 1: Feed Context into Claude
  - Step 2: Tell Claude to Plan a Solution
  - Step 3: Ask Claude to Implement the Solution

# 2025-08-17

今天学习了 MCP 剩下的 resource 和 prompt。
- Resource 相当于在 MCP Server 中提供 data 给到 MCP Client 来获取，也相当于 HTTP 的 Get request，并且会有 official 的 @mcp.resource()，resource 遵循请求-响应模式，MCP Client 发送一个带有 URI 的 ReadResourceRequest 请求，MCP Server 会返回数据。URI 相当于要访问的 resource 的地址。
- Resource 有两种：Direct Resource（URI 是固定的），Templated Resource（URI 会带有 parameters）。
- MCP Server 中的 prompt 允许定义预先构建的高质量指令，供 Client 使用，不需要用户从头开始编写 prompt。相当于可以给到用户几个预定义的指令，但是我们在 Server 中已经写好了对应的高质量的 prompt，这样用户可以直接快速使用指令，不需要每次自定义 prompt。@mcp.prompt()。
- MCP Server 中包含的 Tools，Resources，Prompts 分别服务不同的对象和场景。
  - Tools：Claude 来决定什么时候调用 Tool，结果也是被 Claude 使用。额外功能。
  - Resources：我们的 App 来决定什么时候调用，结果也是被 App 使用。使用数据。
  - Prompts：用户决定什么时候使用。提供简洁指令，基于用户的 input 进行调用。

# 2025-08-16

竟然两天没打卡了！今天跟着视频学习了一下如何 set up 一个 MCP，包括 Client 和 Server。
- 在 MCP Server 中定义所需的 tools，并且 mcp 还提供了一些 official 的 function 可以使用。
- 当把 server 起来后，可以使用 MCP Inspector tool 来 debug，还挺好用的。
- 在 MCP Client 中就是可以获取 tool list 以及调用 tool。
明天继续把剩下的学完。

# 2025-08-13

今天家里网络不是很好，所以就看了两个 MCP 的视频，简单记录一下：
- 模型上下文协议 (MCP) 是一个通信层，它为 Claude 提供上下文和工具，而无需您编写一堆繁琐的集成代码。
- 一个 MCP server 包含了 Tools，Prompts，Resources. 有了 MCP，我们的 server 就不用 implement 那些 Tool 了。
- MCP Client 和 MCP Server 之间是 transport agnostic，支持不同的协议。
- 我比较好奇的是有了 MCP 后的 flow，还是挺复杂的，简单来说就是当用户发起请求时，我们的 server 发送 tools list 请求给到 MCP Client 以及 MCP Server，得到 tool list 后，server 又发送用户请求 + tools list 到 Claude，我们的 server 得到 Claude 的 ToolUse 的回复后，然后再经过 MCP Client -> MCP Server 到外部的服务来得到 tools 的执行结果，我们 server 再把 tool result 发给 Claude，然后 Claude 组织好后最后返回到我们的 server，然后到用户。好繁琐。。。

# 2025-08-12

今天学习了 prompt caching 和 code execution。
- 当 multi-turn conversation 的时候，会把之前的 user message 和 assistant message 再发给 Claude，因为之前已经处理过了，可以使用 prompt caching 来进行缓存，如果要开启 caching，需要添加 cache_control:{"type": "ephemeral"} parameter，并且 caching 还可以用在 system prompt 和 tools，但是 caching 有个最小内容长度的限制：1024 tokens。
- 之前学过 image 和 PDF 的 support，其实还可以先将文件上传到 Claude，Claude 会返回一个 file id，之后发送消息带上 file_id 就可以了。Code execution 不需要自己实现 code，只需要提供一个 code execution tool schema，Claude 会选择的在一个隔离的 Docker 容器中执行 Python 代码，no network access, can execute code multiple times during a single conversation。

今天状态不太好，先这样吧。。。

# 2025-08-10

今天学习了 Extended thinking, Image support 和 PDF support。
- 之前用 Chat-gpt 或者 Claude 都会看到有 Thought process，会把它的思考过程都列出来。当启用 extended thinking 后也会相应的增加 cost 和 latency。启用它的 parameter 是 thinking: {"type": "enabled", "budget": thinking_budget}，thinking_budget 有个最小值是 1024 个 token。
- 当发送 image 给 Claude 时，会在 user message 中多一个 image block，里面包含了 image 的一些信息，比如 type, media_type, data，发送 image 也是有一些要求的，比如单个 request 中不能超过 100 张图，最大 size 不能超过 5MB，对于单个和多个 image 的宽高都有要求，计算发送 image 时所使用的 token 数为：(width px x height px)/750。还有很重要的一点是，发送 image 要配合非常 detailed prompt guides。
- 对于 PDF 来说，比 image 简单一些，因为 PDF 中主要是文字，发送的格式也不太一样。它还提到了一个叫 citations 的 feature，就是在 result 中增加了得出的答案在 PDF 中具体的引用是什么，会让用户感觉更加有可信度吧，可以在前端 build 一个比较好的交互，鼠标移到每个 point 上去显示它具体引用的内容在哪里。当然 citations 也可以用在 plain text 中。

# 2025-08-09

今天学习了 BM25 lexical search, multi-index RAG pipeline, reranking results, 和 contextual retrieval。
- 除了 semantic search 外，今天又介绍了一个 lexical search，并且两种 search 方法可以同时使用，最终将两个部分的 results 进行 merge 合并。lexical search 主要是将关键词出现的次数记录一下，出现的越频繁的 importance 会越低，最终找到包含更多高权重关键词的 section。
- 两种 search merge 的过程：每种 search 对自己的 result text chunk 会有排位 1，2，3，然后最终 merge 后分数的计算是：1.0/(1+ A Rank) +1.0/(1+ B Rank) = 0.xxx，分数越高的即为越接近的 text chunk。
- Re-ranking 就是把 merge 后的结果以及用户问的原始问题组合成一个 promopt 发给 Claude，让它按相关性递减的顺序返回最相关的文档，最好是每个 document tag 里都加个 id，返回的时候就不用将原文返回了，提升一下 Claude 的效率。

# 2025-08-07

今天学习了 Retrieval Augmented Generation(RAG)。
- 最开始从 大型文档+用户问题 的例子出发，讲了两种不同的实现方式
  - 把文档所有内容喂给 AI，让 AI 根据内容来找到答案，但这种方式会很慢并且很贵
  - 第二种就是把文档内容根据一定的规则拆分成 chunk，然后看哪个 chunk 跟用户问的问题最接近。这就是 RAG，它的特点是：Many upsides, many downsides; Many technical decisions to be made; Requires preprocessing; Requires a 'search' mechanism
- Chunking strategies, 如何将文档内容拆分成 chunk
  - Size Based: 根据长度来拆分 chunk，但有可能会把单词或语句拆分在不同的 chunk，就比较难理解，当然也可以设置 overlap 的长度，可以稍微降低一些强行拆分的情况。但我还是觉得这种方式太粗暴了。
  - Structure Based：根据一定的 format 把文档拆分开，通常是根据不同的 heading/section 来拆分，这个比较 make sense，但是这是需要这个文档是有一定清晰结构的。
  - Semantic Based：根据句子/section 内容相关性进行拆分，这部分讲的比较少。
- Text embeddings，将 chunk 通过 Embedding Model 转成 Embedding(list of numbers)，课程里用的是 VoyageAI Embedding Model，当把用户的问题也 Embedding 后，会 compare 用户问题的 Embedding 数字和 chunk Embedding 数字，根据 Cosine Similarity 算法算出哪个 chunk 更接近用户问题。
- RAG 的 workflow：
  - Chunk source text
  - Generate embeddings for each chunk
  - Create a vector store and add each embedding to it
  - Generate an embedding for users question
  - Search the store with the embedding, find the most relevant chunks

# 2025-08-06

今天学习了 text edit tool 和 web search tool，这两个都是 Claude built-in tool，我们只需 pass 非常简单的 schema 就可以。
- text edit tool
  - 它包含了这些功能：View file or directory contents, View specific ranges of lines in a file, Replace text in a file, Create new files, Insert text at specific lines in a file, Undo recent edits to files.
  - 什么时候适合用这个 tool：在你的代码里需要编辑文件时，没法使用功能齐全的代码编辑器时，希望将文件编辑功能集成到 Claude 驱动的应用中时。
- web search tool
  - 它可以直接上网搜索你 request 的内容，schema 也非常简单，可以设置 max_uses，它限制了 Claude 可以执行搜索的次数，还有一个 allowed_domains 是可以限制 Claude 只在哪些 domain 里进行搜索。
  - 在搜索返回数据里，有一个 citations block，它提供了 Claude 搜索这些内容的来源，这些信息可以用来在前端展示，告诉用户 Claude 搜索内容的来源以及用户可以扩展了解。

# 2025-08-05

今天学了 batch tool, tools for structured data 和 fine grained tool calling. 
- 用 batch tool 可以通过一个 single message 让 Claude make multiple tool calls，然后我们的 server 同时处理完后将所有结果一次返回给 Claude，提升了一些效率吧。
- Claude 本身是会有 JSON validation 的，生成的 chunk 不会立马返回给 server，必须要等到 JSON 通过了 validation 后才会将拼接好的 chunks 返回，我们收到返回需要等待一些时间。但如果我们 enable fine_grained，chunk 会立马返回，当然这个时候就没有 JSON validation 了，所以你得到的可能只是一个 string，你的 server 要做好数据结构的处理。

# 2025-08-03

今天学了一下 Multi-turn conversations with tools，没有跟着敲代码，只是看视频了解了一下流程，学到几个知识点:
- 当 Claude 停止返回 response 的时候，会有一个 stop_reason，我们可以根据 stop_reason 的 value 来去判断接下来的操作，看了一下文档：https://docs.anthropic.com/en/api/handling-stop-reasons#stop-reason-values，有 6 个 value：end_turn, max_tokens, stop_sequence, tool_use, pause_turn, refusal。本节课程里用到的是 tool_use，就是需要我们的 server 来 execute tool function 了。
- 示例里的 run_conversation function 其实就是写了一个 while loop，当判断 Claude response stop_reason 不是 tool_use 的时候就退出结束对话。期间还涉及到了 ToolUseBlock(id, input, name, type("tool_use")) 和 ToolResultBlock(too_use_id, content, is_error, type("too_result"))。

# 2025-08-02

今天学习了 Tool use with Claude, 当 AI 有些内容回答不了（特别是一些实时的信息）的时候需要借助人类提供的 Tool 来解决，并通过 message 来回组合成 final result。
- Tool function 创建的时候也要遵循一些 best practices，各种表述要清晰，input、error 等要 meaningful，并且要有比较好的 input validation。
- 还有一点是要定义一个 JSON Schema，用来描述你的这个 tool function 的实现，包含了 name, description, input_schema, input type, input properities, input required 等信息，让 AI 能更好的理解你的这个 tool，这块特别像后端给前端提供的 API 定义，让调用者理解如何使用这个 API。
- 流程就还是，发送 message 到 Claude 时带上 tool attribute，value 事 tool function 的 schema，得到 Claude message 返回时，再将 tool function 的结果返回给 Claude，最终 Claude 返回最终的 message。但要记住每次 send message 都需要将 tool schema 带上。

我还是觉得有点傻，人类用代码实现的 function 得到的结果发给 Claude 然后再发回来。。。

# 2025-07-31

今天学习了 structure with XML tags 和 providing examples。
- structure with XML tags：用语义化的 XML tag 将大段的文字包裹起来，让 AI 能够更好的理解其中的意思。
- providing examples：给出明确的 input 和 output 的例子，让 AI 能更好的 get 到你想要的结果，特别是对复杂的问题，examples 还分为 “One-shot” 和 “Multi-shot”。
- 感觉还是不能把 AI 当作“成熟复杂的人”，原则还是尽可能的表达直接、简单、清晰、结构化、给出足够的 context 甚至例子。

想起了之前奥特曼说大家不要使用一些礼貌用语，会增加 AI 的计算量且没啥用处，确实，它不会因为你的“请”“谢谢”而给你更加优质的 response。

# 2025-07-30

今天学习了 Prompt engineering techniques, 有几点需要注意：
- 提高 prompt 的 process：
  - Set a goal -> Write an initial prompt -> Eval the prompt -> Apply a prompt engineering technique -> Re-eval to verify better performance，然后 repeat 最后两步直到得到一个满意的结果。
- 如何写好一个 prompt，需要遵循以下三个原则，其实还是要把 AI 当“机器”，你要给它下指令，指令描述必须非常清晰，或者有清晰的步骤，其实也是教 AI 如何思考，需要把我们觉得特别注意的点明确的告知 AI
  - Be clear
    - Use simple language
    - State what you want explicitly
    - Lead your prompt with a simple statement of the model's task
  - Be direct
    - Use instructions, not questions
    - Use direct action verbs("Write", "Create", "Generate")
  - Be specific
    -  guideline types:
       - list qualities that the output should have
       - provide steps the model should follow

# 2025-07-29

- 今天学习了 Prompt evaluation，主要是通过一个 eval workflow 来检测你当的 prompt 的质量，通常会通过一个 score 来体现。这个 workflow 的流程大概就是：Draft a Prompt → Create an Eval Dataset → Feed Through Claude → Feed Through a Grader → Change prompt and Repeat。在 Grader step，它还提到了三种不同的评分体系：Code(Programmatically evaluate the result), Model(Ask a model to assign a score to the output, or compare two versions), Human(Ask a human to assign a score to the output, or compare two versions)，可以结合起来获得一个平均分。
- 最后的 Quiz 又错了一道题. You need test cases for your prompt evaluation. You have two options: write them by hand or use Claude to generate them. Which model should you use for generation? 应该选 A faster model like Haiku，我选的是 The same model you're testing，应该是那一段没好好听，确实，就是 generate 一些 test cases, 选最快的 Model 就行了，可能是例子中没有切换 Model，所以印象不深刻。

# 2025-07-27

- prefilled assistant message，它是可以给 Clause 的回答一个 direction 的，所以在一些 App 里，即使引用的是通用的 LLM，App 本身也是可以做很多事情来控制输出结果的。
- stop sequences, 目前没想到什么特别适合的使用场景，虽然后面提到了 structured data 里会用到，但还是想看看后续有没有什么更加有用和适合的场景吧。
- structured data，课程里用 prefilled assistant message 和 stop sequences 组合使用的方式来解决 json + markdown 的返回格式的问题，但是我的第一想法不应该是直接在 prompt 里或者 system prompt 说明来的更直接简单一点吗？
- Quiz 打错了一个题，temperature 记反了，再复习一下：
  - Low Temperature(0.0 - 0.3): Factual responses, Coding assistance, Data extraction, Content moderation
  - Medium Temperature(0.4 - 0.7): Summarization, Educational content, Problem-solving, Creative writing with constraints
  - High Temperature(0.08 - 1.0): Brainstorming, Creative writing, Marketing content, Joke generation

今天的学习先到这了，滚去修 bug 了......

# 2025-07-25

今天学习了 system prompts, temperature, 和 response streaming，也明白了很多 AI Chat App 的一些背后实现逻辑，只是在调用 API 时传入不同的参数或者调用不同的方法来实现。特别是 response streaming，为了提高用户体验，不让用户等太久，可以使用 stream 的方法来按顺序一段一段地输出结果。

# 2025-07-24

- 今天学习了第一个课程 Claude with the Anthropic API, 课程内容还是非常清晰易懂的，介绍了 Claude 目前三种模型的不同，以及开发过程中如何选择。
- 创建了 Anthropic API Key，跟着课程用 Python 试了一下在 chat 场景下的 API call，还是挺简单有意思的。
- 几年前想学 Python，被它很蹩脚的语法劝退了哈哈，没想到现在又要重新写了。
- 其中还有一个点是 Claude API 不记录 message 记录，所以在 multi-turn conversations 的场景下，要把之前的 message 历史记录全部发过去。

<!-- Content_END -->
