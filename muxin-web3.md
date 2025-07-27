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
