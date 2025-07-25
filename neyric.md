---
timezone: UTC+8
---

# Neyric

**GitHub ID:** neyric

**Telegram:** @neyric68

## Self-introduction

A coder who is passionate about web technologies.

## Notes

<!-- Content_START -->
# 2025-07-25

## 模型区别

之前只知道有 Opus / Sonnet / Haiku 这三个模型，但是他们的区别在哪里不甚了解（只知道 Opus 最贵、Haiku 最便宜，没有细致去了解过）现在此记录一下：

- Haiku：速度最快的模型，价格比较便宜，不具备推理能力
- Sonnet：在快速和高智能之间的一个折中方案，具备推理能力，但思考不如 Opus 深入
- Opus：推理能力强，但是需要一定的时间思考，适合完成复杂任务（例如编写一个复杂的查询 SQL 语句）

初步理解下来，Haiku 的应用场景大概适合复杂度不是那么高的任务上，Sonnet 适合日常使用，Opus 适合在处理一些 Sonnet 解决不好的场景上。

## API 安全

课程里提到一句 `You should never make requests to the Anthropic API directly from client-side code` 这个很重要，绝对不要把 API Key 暴露在用户端。

## Claude 的处理逻辑

发送给 Claude 的一段内容，在内部会依照着 4 个阶段进行处理：Tokenization => Embedding => Contextualization => Generation

在 Embedding 阶段会推测该 Token 的含义，再结合上下文生成结果。

是不是意味着，通过使用更明确的 Word（例如：Kiro 在做 SPEC 时会使用的 WHEN / WHEN / SHALL 等关键词）来编写 Prompt 可以得到更好的结果？

## 获取 API 密钥

教学里介绍了从 Anthropic 官方获取 API 密钥使用，不过我选镜像，原因如下：

1. 充值困难，要在 Anthropic 花钱，不支持国内发行的信用卡，得通过虚拟卡来充值
2. 调用 API 困难，需要给应用设置 Agent 才能成功连接到官方 API

还有一点，镜像的价格比官方便宜（也有同价，便宜一点的稍微有那么一点不稳定）

# 2025-07-24

<!-- Content_END -->
