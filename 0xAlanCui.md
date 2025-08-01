---
timezone: UTC+8
---

# Alan C

**GitHub ID:** 0xAlanCui

**Telegram:** @alan4sg

## Self-introduction

Experimenting on something new in AI Agent.

## Notes

<!-- Content_START -->
# 2025-08-01

# 📘 Anthropic Courses Day 7 学习笔记

## ✅ 今日进度
- 正式完成「Tool Use with Claude」模块的最后 3 个小节 ✅
  - The text edit tool
  - The web search tool
  - Quiz on tool use with Claude
- 成功通过章节测验，**7 题全对（100%）**！

## 💡 今日收获
- **Text edit tool**：Claude 能够理解编辑指令，对输入文本进行结构化修改（如润色、摘要、翻译等），特别适合处理自然语言编辑类任务
- **Web search tool**：Claude 可以调用搜索工具访问外部信息，结合提示词，能实现更强的实时问答能力
- 工具调用和 prompt 相结合后，可以构建更复杂的 Agent 流程，例如：提问 → Claude 搜索 → Claude 总结 → Claude 回复


## 🎯 明日目标
- 开始进入下一个模块 Retrieval Augmented Generation

# 2025-07-29

# 📘 Anthropic Courses Day 6 学习笔记

## ✅ 今日进度
- 继续推进「Tool Use with Claude」模块，今天学习了中后段的 6 个小节 ✅
  - Multi-turn conversations with tools
  - Implementing multiple turns
  - Using multiple tools
  - The batch tool
  - Tools for structured data
  - Fine grained tool calling

## 💡 今日收获
- Claude 的工具调用不仅支持单轮对话，还能完成复杂的多轮对话任务
  - 可以跨多轮使用同一个工具，也可以在不同回合中调用不同工具
- Claude 支持并发调用多个工具（multi-tool），适合执行多个子任务
- **Batch tool** 和 **structured data tools** 帮助在处理大量或结构化信息时提高效率
- **Fine-grained tool calling** 支持开发者对工具调用过程进行更细致的控制，例如限制调用频率、触发条件等

## 💤 小遗憾
- 今天未能完成本章节的所有内容

## 🎯 明日目标
- 学完 Tool Use 最后 3 个小节并完成测验

# 2025-07-28

# 📘 Anthropic Courses Day 5 学习笔记

## ✅ 今日进度
- 进入第四章节「Tool Use with Claude」，学习了前半部分的 6 个小节 ✅
  - Introducing tool use
  - Project overview
  - Tool functions
  - Tool schemas
  - Handling message blocks
  - Sending tool results

## 💡 今日收获
- Claude 支持原生的 Tool Use（工具调用）机制，是其高级能力的核心特征之一
- 学习了 Tool Use 的基本原理和工作流程：
  - 可以将外部函数作为“工具”注册给 Claude
  - Claude 会根据上下文自主决定是否调用工具、如何调用工具
  - 工具调用返回结果后，可以通过 `tool_use` block 发送回 Claude 进行进一步处理
- Claude 支持以 JSON schema 的形式定义工具结构，增强调用时的准确性与安全性

## 🔍 一些关键概念
- **Tool schema**：定义工具参数、类型、描述等，用于自动生成调用格式
- **Message block**：Claude 与用户/工具之间的通信内容格式（如 user/tool/result）
- **Tool result**：工具执行后的响应结果，通过指定 block 传回 Claude 处理

## 🎯 明日目标
- 学完 Tool Use 剩下的部分内容
- 完成章节测验

# 2025-07-27

# 📘 Anthropic Courses Day 4 学习笔记

## ✅ 今日进度
- 完成第三章节「Prompt Engineering Techniques」的全部课程 ✅
- 本章共包含 7 个小节，已全部学完：
  - Prompt engineering
  - Being clear and direct
  - Being specific
  - Structure with XML tags
  - Providing examples
  - Exercise on prompting
  - Quiz on prompt engineering techniques
- 顺利通过测验，**5 题全对（100%）**！

## 💡 今日收获
- 高质量的 prompt 有几个核心技巧：
  - **明确清晰**：避免模糊描述，减少模型误解
  - **具体**：越具体越能引导 Claude 输出你想要的结果
  - **结构化**：使用 XML 或其他结构标签有助于系统性理解和处理
  - **提供范例**：举例能显著提高

# 2025-07-26

# 📘 Anthropic Courses Day 3 学习笔记

## ✅ 今日进度
- 顺利完成第二章节「Prompt Evaluation」的所有课程内容
- 包括以下 7 个小节：
  - Prompt evaluation
  - A typical eval workflow
  - Generating test datasets
  - Running the eval
  - Model based grading
  - Code based grading
  - Exercise on prompt evals
- 成功通过章节测验，**6 题全对（100%）**！🎉

## 💡 今日收获
- Prompt 的好坏可以通过自动化手段进行评估，不再完全依赖人工肉眼观察
- Anthropic 提供两种评估方式：
  - **Model-based grading**：由 Claude 自己对生成内容评分，更高效也更客观
  - **Code-based grading**：适用于有明确标准答案的任务
- 测评工作流强调了 prompt 的迭代过程：
  1. 构造 prompt → 2. 生成数据集 → 3. 执行评估 → 4. 优化 prompt → 循环往复

## 🧠 理解加深
- Claude 不只是 LLM，还支持更专业的 prompt 工程流程
- Prompt 工程不仅是“写提示词”，而是可以通过量化指标和系统流程持续优化的「工程实践」

## 🎯 明日目标
- 开始学习下一个章节「Prompt engineering techniques」

# 2025-07-25

# 📘 Anthropic Courses Day 2 学习笔记

## ✅ 今日进度
- 正式完成第一章节「Accessing Claude with the API」的所有课程内容 ✅
- 顺利通过章节测验，**8 题全对（100%）**！
- 开始进入第二章节「Prompt Evaluation」，已学习前两个小节：
  - Prompt evaluation
  - A typical eval workflow

## 💡 今日收获
- Claude 不只是能聊天，Anthropic 也提供了用于评估 prompt 表现的工具与思路
- 评估流程大致包括：
  1. 设计 prompt
  2. 生成或准备测试数据集
  3. 运行评估（自动化或人工）
- 好的评估流程可以帮助我们迭代提示词，从而提升 Claude 的稳定性与输出质量

## 🎯 明日目标
- 学完 Prompt Evaluation 剩下的全部内容
- 如果有时间，尝试手动跑一次简单的 prompt 测试流程

# 2025-07-24

# 📘 Anthropic Courses Day 1学习笔记

## ✅ 今日进度
- 正式开始学习 Anthropic Courses 的第一天
- 已注册并支付 $5 美元，成功获得 API Key
- 学完以下基础章节内容：
  - Claude 模型概览
  - 如何访问 API
  - 获取 API Key
  - 发起请求
  - 多轮对话结构
  - 系统提示词（System Prompts）
  - 温度（Temperature）
  - 流式响应（Response Streaming）
  - 控制模型输出的方式

## 💡 今日收获
- `temperature` 可以控制模型回答的创造力，数值越高越发散，或者说越有创造力
- 支持流式输出，适合做实时交互体验，让用户感受更丝滑

## 🧪 遇到的问题
- 在尝试运行课程里的示例代码时遇到环境问题（Python 环境/依赖未配置好）😞
- 尚未成功跑通第一个 API 请求 😭


今天主要是理解 Claude API 的基础结构和功能，动手部分还没完全搞定，但思路清晰了，期待明天能顺利调通代码


<!-- Content_END -->
