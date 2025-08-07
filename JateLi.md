---
timezone: UTC+12
---

# JateLi

**GitHub ID:** JateLi

**Telegram:** @bot_zhe

## Self-introduction

小小纽村前端程序员，乐于学习新技术，请多指教：）

## Notes

<!-- Content_START -->
# 2025-08-07

Using Multiple Tools = 在现有工具框架基础上添加更多工具，让Claude拥有更丰富的能力

Fine-Grained Tool Calling 本质上是在响应速度和数据可靠性之间的权衡选择，适合对实时性要求较高的交互式应用。

TestDone

# 2025-08-05

Tool Schema = Claude的"工具说明书"

Message Blocks = 把简单的"一问一答"变成了"解释+行动+结果"的复杂对话流程。

Tool Results = 让Claude能够"看到"工具执行的结果，从而基于真实数据做出准确回应。

流程：Claude提问 → 工具执行 → 结果返回 → Claude整合答案

Multi-Turn Tool Conversations = Claude在一次用户请求中，连续使用多个工具来完成复杂任务

Diff：
单轮对话: "告诉我天气" → 一次工具调用
多轮对话: "帮我规划明天的行程" → 查天气 + 查日程 + 推荐活动 + 设置提醒

# 2025-08-04

### Tool Use with Claude ###

Claude调用外部工具获取实时信息和执行操作
1. 用户请求 → 2. Claude判断需要工具 → 3. 调用工具 → 4. 获取结果 → 5. Claude整合回答

1. Tool Functions
给Claude提供它自己做不到的能力
Claude = 大脑（思考、理解、规划）
Tool Functions = 手脚（获取信息、执行操作）

# 2025-08-03

Prompt Engineering = 系统性地编写和优化提示词，让AI给出更好的回答

1. Clear and direct
2. Being specific 
3. XML标签结构
4. 提供示例: One-shot & Multi-shot: 多个示例，处理复杂情况

Quiz on prompt: 5/5

# 2025-08-01

Model Based Grading
1. Code Graders: 程序化检查快速、客观(仅技术指标)
2. Model Graders:  API调用评估高度灵活(可能不一致)
3. Human Graders:人工评估最准确(耗时、昂贵)

Code Based Grading
1. 准备测试数据集（包含format字段）
   ↓
2. 运行run_eval函数
   ↓ 
3. 每个测试用例同时进行：
   - Model Grading（语义质量1-10分）
   - Code Grading（语法有效性0/10分）
   ↓
4. 计算综合分数：(模型分数 + 语法分数) / 2
   ↓
5. 生成评估报告：平均分、通过率、详细结果
   ↓
6. 基于结果优化提示词，重复评估

Quiz on prompt evaluation: 6/6

# 2025-07-31

Prompt Evaluation
使用客观指标自动测试提示词效果的技术，系统性地改进提示词质量。（不依赖主观感受）

Typical Eval Workflow
步骤1：编写初始提示词草稿 
    ↓
步骤2：创建评估数据集（3个或数千个测试用例）
    ↓  
步骤3：生成提示词变体（将测试输入插入模板）
    ↓
步骤4：获取LLM响应（发送给Claude收集输出）
    ↓
步骤5：对响应进行评分（1-10分制，计算平均分）
    ↓
步骤6：迭代改进（修改提示词，重复循环，对比版本）
核心：客观分数 + 系统性测试 + 量化改进

# 2025-07-29

Temperature = 0
完全确定性输出
始终选择概率最高的词汇
每次运行相同输入会得到相同输出
最保守、最可预测

Temperature 接近 1
增加选择低概率词汇的机会
输出更有创意和意外性
相同输入可能产生不同输出
更随机、更有变化


Quiz passed 8/8

# 2025-07-28

# 第二天：Multi-Turn Conversations and System Prompts

Claude API的关键限制是它不会自动存储任何消息

"role"：要么是"user"要么是"assistant"
"content"：实际的消息文本

Helper functions:
add_user_message(messages, text) - 将用户消息添加到历史中
add_assistant_message(messages, text) - 将Claude的回复添加到历史中
chat(messages) - 将完整的消息历史发送给API

Basic Chat:
1. 向Claude发送初始用户消息
2. 接收Claude的回复
3. 将Claude的回复添加到消息历史中
4. 将新的用户消息添加到历史中
5. 为下一个请求发送完整的历史记录

System Prompts: 设定一个身份或角色风格

# 2025-07-27

# 第二天：Get API and Make a Request
1. **Generate APK**
2. **Set Up Python Environment**
3. **Make a First Request**

# 2025-07-25

# 第二天：Accessing the API

## API Access Flow (5 Steps)
0. **FE**
1. **Client** → Developer's server (keep API key secret)
2. **Server** → Anthropic API (using SDK or HTTP)
3. **Text Generation Process**:
  - **Tokenization**: breaking input into tokens
  - **Embedding**: converting tokens to numerical representations
  - **Contextualization**: adjusting embeddings based on neighboring tokens
  - **Generation**: predicting next word, repeating process
4. **Stop**: when max_tokens reached or end_of_sequence token generated
5. **Return**: generated text + usage counts + stop_reason

## Core Concepts
- **Token**: text chunk (word/word part/symbol/space)
- **Embedding**: numerical representation of word meanings
- **Contextualization**: meaning refinement using neighboring words
- **Max_tokens**: generation length limit
- **Stop_reason**: why model stopped generating

# 2025-07-24

# 第一天：Claude 与 Anthropic API

## 模型概览

- **Opus**：最强智能，适合复杂推理，成本高、延迟大（如 deep thinking）
- **Sonnet**：性能均衡，编程能力强，适合大多数场景（如 coding）
- **Haiku**：最快最省，适合简单对话，无复杂推理（如 chatbot）
<!-- Content_END -->
