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
# 2025-07-26

## 要提供给 AI 完整上下文

和 ChatGPT 一样，对 Claude API 的每次请求都是一个完整的会话，不存在说提供一个 ID，Claude 就能自动关联到先前的聊天内容。如果要构建一个基于 Chat 的服务，需要自行维护一个 Messages 列表，使用时需要一次性提交给 Claude （不太清楚 Token 消费是怎么计算，有没有缓存命中之类的）

## 系统提示词

系统提示词用于约束 Claude 或者说，让 Claude 专注于某件事情。比方说指定它是一个资深的 Coder、一个 Prompt 优化师又或者是旅行规划师之类的角色，最对它进行对话来得到更符合预期的结果。

和 ChatGPT 不同的是，Claude 的系统提示词是一个单独的 system 参数（ChatGPT 中是维护在 messages 第一条，并有一个特殊的 role）

## Temperature 参数

这是一个介于 0.0-1.0 的参数，使用 Temperature 来控制结果的多样性，较高的 Temperature 得到的结果随机性更高，适合做一些创意性的工作。低的 Temperature 输出的会更加稳定，同一个问题多次询问，低 Temperature 得到的结果应该大致是相近的。

感觉高 Temperature 适合拿来做一些生成式的内容，比方说 Prompt 生成，低 Temperature 适合用来写代码。

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

## 开发以及接入 API

课程里建议使用 Python 进行开发，目前初步看了一下 Making a request 的课程，决定选用自己习惯的 TypeScript 开始，而且 Anthropic 提供了 TypeScript 的 SDK 直接装包就好了，而且后续需要开发用户界面的时候，也可以直接用 SSR 完成，部署到线上也方便。

Github Repo：https://github.com/neyric/claude-course

项目使用 Bun + TypeScript 开发，要是有同学想使用，只需要把 `.env.example` 重命名为 `.env` 并填写 API_KEY 即可。

# 2025-07-24

<!-- Content_END -->
