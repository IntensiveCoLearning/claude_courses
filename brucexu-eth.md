---
timezone: UTC+12
---

# Bruce Xu

**GitHub ID:** brucexu-eth

**Telegram:** @brucexu_eth

## Self-introduction

在看 AI 的 E 卫兵

## Notes

<!-- Content_START -->
# 2025-07-25

## Coding

今天开始实战，所以我创建的代码不放在笔记了，放在这个仓库里面 <https://github.com/brucexu-eth/claude-courses-code>

重点笔记：

- Anthropic 是 stateless 的，所以如果你要关联上下文，就必须把上下文都提供进去。这也是为什么 claude code 需要时不时 compact 一下，因为每次发送都是整个上下文都发。所以官方提供建议从大往小，然后专注某一个具体的事情，不要同时做
- system prompts 用于设置 role 或者指示 AI 用什么用的姿态来思考和回复
- Temperature 修改选择 token 的概率，从 0 - 1，如果 0 则始终选择最高概率的那个 next word，否则就是更加随机。所以需要在不同的使用场景来选择不一样的，比如 解析数据和处理用 0，脑暴创意用 1

# 2025-07-24

## https://anthropic.skilljar.com/claude-with-the-anthropic-api

看了下课程，似乎是有几个小的 modules 然后组合在一起形成不同的课程，看了下这个应该是最全的，可以从零到一开发整个 AI 项目的，所以先 enroll 了这个课程。

看了下课程信息量好大，感觉这次学完这个就足够了。

### Claude models

- Opus: highest level of intelligence
- Sonnet: balances quality, speed, cost
- Haiku: Most cost-efficient and latency-optimized model (no resoning)

Combine together: use Opus to design the product and Sonnet to code and Haiku for user interact.

### Accessing the API

Parameters:

- API Key
- Model
- Messages
- Max Tokens: Limit to how many "tokens" the model can generate

Process of LLM:

- Tokenization: sentences -> words -> tokens
- Embedding: each token -> (-0.34, 0.87, 0.15 ...) - numbers represent all possible meanings of each words
- Contextualization: adjust embedding based on other embeddings around, because the meaning of the word might be changed in the context. The final embeddings sent to Output Layer to make predict to the next word (highest probability) -> a mix of probability and randomness to select words -> natural and varied responses -> added to the last of the list of embeddings and repeat the process
  - Have we exceeded the max tokens?
  - Did we generate a token matching a stop sequence? -> Model thinks this is the end of the response
  - Did we output an End of Sequence token?
- Generation text

API Response:

- Message
- Usage: Number of input + output tokens
- Stop Reasons: Why the model stopped generation

TODO https://anthropic.skilljar.com/claude-with-the-anthropic-api/287725
<!-- Content_END -->
