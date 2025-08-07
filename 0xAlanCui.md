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
# 2025-08-07

# 📘 Anthropic Courses Day 12 学习笔记

## ✅ 今日进度
- 正式进入「Model Context Protocol（MCP）」模块的学习 ✅
- 今日已完成前 4 个小节内容：
  - Introducing MCP
  - MCP clients
  - Project setup
  - Defining tools with MCP

## 💡 今日收获
- **MCP（Model Context Protocol）** 是 Anthropic 提出的“模型上下文协议”，目的是为 Claude 提供结构化、模块化的上下文与资源注入方式
- **MCP clients**：允许多个客户端共享统一的上下文、工具、资源和 prompt，有利于构建跨团队协作的 Agent 系统
- **项目初始化流程**：
  - MCP 项目通过一套标准结构组织工具、资源、prompt 等组件
  - 支持本地开发 + 云部署，方便在不同环境中快速复用
- **工具定义（Defining tools with MCP）**：
  - 工具（Tool）在 MCP 中可独立定义、统一注册、结构清晰
  - MCP 提供 schema 驱动的方式管理工具逻辑，便于后续维护与扩展

## 🧠 理解要点
- 相较于之前的裸 API 调用，MCP 更像是为 Claude 构建了一个“多模态插件系统”框架
- MCP 项目结构是 Claude 作为 Agent Runtime 的基础设施，尤其适用于需要持续演进、多人协作的 AI 产品

## 🎯 明日目标
- 继续学习 MCP 模块剩余内容：
  - Server inspector、资源访问、Prompt 配置等
  - 完成 MCP 总结与测验

# 2025-08-05

# 📘 Anthropic Courses Day 11 学习笔记

## ✅ 今日进度
- 完成「Features of Claude」模块的全部课程 ✅
- 今日学习了剩余 4 个小节内容：
  - Prompt caching
  - Rules of prompt caching
  - Prompt caching in action
  - Code execution and the Files API
- 成功通过章节测验，**7 题全对（100%）**！

## 💡 今日收获
- **Prompt caching**：可以显著减少重复请求的计算开销，对于重复性任务（如同一 prompt 不断调用）非常高效
- 缓存规则：
  - 仅支持 deterministic 模型调用（不可使用 temperature、top_p 等采样参数）
  - 不缓存 Tool Use、Streaming、非 JSON 格式等复杂请求
- **Code execution & Files API**：Claude 支持基本代码执行功能和文件上传处理，未来可用于多轮交互中处理文件（如读取 PDF、CSV、运行脚本等）

## 🧠 模块总结
- 这一章功能非常实用，为 Claude 接入到生产环境中的“稳定性 + 性能 + 文件处理”能力打下基础
- Prompt 缓存是构建低延迟高性能 Agent 的关键

## 🎯 明日目标
- 进入「Model Context Protocol（MCP）」模块，开始学习 Claude 的上下文注入与共享机制

# 2025-08-04

# 📘 Anthropic Courses Day 10 学习笔记

## ✅ 今日进度
- 开始学习「Features of Claude」模块，已完成前 4 个小节 ✅
  - Extended thinking
  - Image support
  - PDF support
  - Citations

## 💡 今日收获
- **Extended thinking**：Claude 可以根据任务复杂度进行更深入的推理，例如处理更长上下文或多步骤思考任务，支持设置 `max_tokens`
- **Image support**：Claude 可接收图像输入（base64 编码或 URL），用于图像识别、图文理解等多模态场景
- **PDF support**：Claude 可以直接处理 PDF 文件
- **Citations（引用）**：Claude 支持在输出中自动生成引用信息，适合问答、研究、知识型产品等需要“可追溯来源”的场景

## 🔍 关键理解
- 这些功能是 Claude 向“通用 AI 助手”迈进的重要能力，尤其是 PDF + 图像处理结合 RAG 后非常有想象力
- 自动引用机制意味着 Claude 能在生成内容时增加“可信度”标签，有利于减少幻觉输出（hallucination）

## 🎯 明日目标
- 学完 Features 模块剩余内容：
  - Prompt caching 机制与实战
  - Code execution 与 Files API 支持
  - 完成章节测验

# 2025-08-03

# 📘 Anthropic Courses Day 9 学习笔记

## ✅ 今日进度
- 正式完成「Retrieval Augmented Generation（RAG）」模块全部课程内容 ✅
- 今日学习了剩余的 5 个小节内容：
  - Implementing the RAG flow
  - BM25 lexical search
  - A Multi-Index RAG pipeline
  - Reranking results
  - Contextual retrieval
- 顺利通过章节测验，**8 题全对（100%）**！

## 💡 今日收获
- **BM25** 是一种基于关键词的经典检索方式，适合低成本初步匹配，搭配 embedding 可组成 hybrid 检索系统
- **多索引管线（Multi-Index RAG pipeline）**：结合多个向量索引（如按语言、结构、内容来源等），提升召回广度
- **Reranking（重排序）**：基于查询与文档的语义关系，用 Claude 对检索结果排序，提升最终输出质量
- **Contextual retrieval**：将上下文对检索过程的影响纳入考虑，提升针对复杂对话任务的表现

## 🧠 模块理解总结
- RAG 不只是“检索+拼接+生成”，而是一个可以不断优化、组合策略的智能知识注入系统
- Claude 在整个 RAG 流程中可以参与多个环节（embedding、检索、排序、生成），适合构建高质量的企业级问答系统

## 🎯 明日目标
- 准备进入下一个章节「Features of Claude」，探索如 Extended Thinking、图像支持、PDF 理解等能力

# 2025-08-02

# 📘 Anthropic Courses Day 8 学习笔记

## ✅ 今日进度
- 正式进入「Retrieval Augmented Generation（RAG）」模块的学习 ✅
- 已完成前 4 个小节：
  - Introducing Retrieval Augmented Generation
  - Text chunking strategies
  - Text embeddings
  - The full RAG flow

## 💡 今日收获
- RAG 是结合 **检索（Retrieval）+ 生成（Generation）** 的增强式 AI 架构，适用于需要外部知识的问答或文档总结任务
- Claude 支持 RAG 组件的构建，流程包含：
  1. **文本切分（chunking）**：将长文档按策略分段，便于后续索引和匹配
  2. **文本向量化（embedding）**：将 chunk 编码为向量，供相似度搜索使用
  3. **检索匹配 + 输入拼接**：基于 query 检索相关 chunk 拼入 Claude 输入中
- RAG 的整体流程重点在于“选出正确的信息”和“合理组织输入格式”

## 🔍 印象深刻的点
- Chunking 不只是按字数切分，还可以用段落、句子等语义方式组织
- Claude 使用的 embedding 与外部 vector store 配合可以实现灵活的 hybrid 检索系统

## 🎯 明日目标
- 学完 RAG 模块剩下的小节
- 完成章节测验

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
