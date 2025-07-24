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
<!-- Content_END -->
