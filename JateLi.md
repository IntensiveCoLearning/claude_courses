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
