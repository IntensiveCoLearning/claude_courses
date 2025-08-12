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
