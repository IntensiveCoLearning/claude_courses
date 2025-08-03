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
