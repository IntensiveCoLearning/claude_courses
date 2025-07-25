---
timezone: UTC+8
---

# ray

**GitHub ID:** rayjun

**Telegram:** @rayoo_eth

## Self-introduction

A Builder

## Notes

<!-- Content_START -->
# 2025-07-25

MCP 的设计思想符合软件工程中的经典做法，**没有什么软件上的问题是增加中间层解决不了的问题，如果有，那就再加一层**。增加中间层的本质就是屏蔽复杂性，既然用户享受到了 MCP 的便利性，那么复杂性自然由提供 MCP 的人来承担，Java 语言中的 JVM，以太坊中的 EVM 都是类似的设计思想。

MCP Client 和 MCP Server 之间的交互依然是一个很经典的协议设计，在 MCP Client 在提出具体的请求之前，会先确认 MCP Server 有哪些能力，然后再从中挑选一个当前用户请求最需要的能力去做具体的事情。这种设计在网络协议的设计中也很常见，比如在以太坊的两个节点之间交互时，会先发送请求去确认当前节点支持的协议版本，然后从中挑选两个节点双方都支持的一种协议开始后续的交互。

# 2025-07-24

今天开始在看 MCP 的视频，突然想到另外一种理解 MCP 的方式，其实某种程度上，大模型本身也是封闭的，持有的信息只到它被生成的那一刻，从那以后就是一个封闭的系统，从这个角度上来看和区块链是一致的。它们的相似点在于：

- 大模型和区块链都有获取现实世界新信息的需求，而无法依靠自身做到
- 区块链可以依赖预言机，而大模型依赖 MCP，本质上都是讲数据标准化，然后以系统能够理解的方式传到大模型或者区块链
- 这个需求会持续存在，但真正有价值的还是数据，而不是预言机或者 MCP 本身


顺便说一下，当前这个学习打卡的模式真的太丝滑了，非常适合新手，如果能优化一下 makrdwon 的语法提示和编辑就更棒了。
<!-- Content_END -->
