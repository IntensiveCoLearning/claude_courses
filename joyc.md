---
timezone: UTC+9
---

# hython

**GitHub ID:** joyc

**Telegram:** @hython

## Self-introduction

web3 从业者，AI 爱好者

## Notes

<!-- Content_START -->
# 2025-07-26

# 🧠 第五课：构建 Claude 的上下文记忆机制

## 📌 课程目标：

- 理解 Claude API 的无状态性。
- 学习如何手动维护消息上下文。
- 编写可复用的助手函数，实现多轮对话能力。


## ❗ Claude 是无状态的

Claude（以及 Anthropic API）不存储对话历史，不保存用户发送的消息和 Claude 返回的回复。

因此，若想实现多轮上下文对话，需：

1. 在本地维护完整的消息列表（包括用户和助理）。
2. 每次请求都提交完整的消息列表。


## 🔁 示例说明：错误的对话续写方式

### ❌ 错误方式（无上下文）：

```python
message = client.messages.create(
    model=model,
    max_tokens=1000,
    messages=[{
        “role”: “user”,
        “content”: “Write another sentence.”
    }]
)
```

结果：Claude 无法关联前文，返回与“量子计算”无关的内容。


## ✅ 正确方式：维护完整消息历史

### 示例结构（伪代码）：

```python
messages = [
    {“role”: “user”, “content”: “What is quantum computing?”},
    {“role”: “assistant”, “content”: “Quantum computing is …”},
    {“role”: “user”, “content”: “Write another sentence.”}
]
```

每次调用 Claude API，都传入上述完整 `messages`。


## 🛠️ 创建 3 个复用助手函数

### 1. 添加 User 消息：

```python
def add_user_message(messages, text):
1. 添加用户消息：

```python
def add_user_message(messages, text):
    user_msg = {“role”: “user”, “content”: text}
    messages.append(user_msg)
```

2. 添加Assistant消息：

```python
def add_assistant_message(messages, text):
    assistant_msg = {“role”: “assistant”, “content”: text}
    messages.append(assistant_msg)
```

3. 执行Claude对话请求：

```python
def chat(messages):
    response = client.messages.create(
        model=model,
        max_tokens=1000,
        messages=messages
    )
    return response.content[0].text
```

## 💬 多轮对话示例

```python
# 初始化上下文
messages = []

# 第一句提问
add_user_message(messages, “Define quantum computing in one sentence.”)
answer = chat(messages)
print(answer)

# 保存 Claude 的回复
add_assistant_message(messages, answer)

# 第二轮提问
add_user_message(messages, “Write another sentence.”)
answer = chat(messages)
print(answer)
```

Claude会输出一条延续前一条回答的句子，确保上下文连贯。

## 🧠 小结

- Claude是无状态模型，每轮请求都需提交完整上下文；
- 实现了3个可复用的辅助函数：add_user_message、add_assistant_message和chat；
- 支持构建连续自然的多轮交互体验；
- 后续课程将继续基于此框架深入构建高级Agent。
- 后续代码将提交至[github](https://github.com/joyc/anthropic-claude-course)

# 2025-07-25

# 第一节：课程导论（Course Introduction）

## 👨‍🏫 讲师信息
- 姓名：Stephen Greider
- 公司：Anthropic，技术团队成员

---
## 🗂️ 本课程涵盖内容
1. Claude 模型介绍
2. Anthropic API 使用
3. Prompt 工程与评估
4. 工具调用（Tool Use）
5. RAG（检索增强生成）
6. MCP（Model Context Protocol）扩展服务
7. 官方 Agents：Cloud Code & Computer Use
8. 多步骤任务与 Agent 工作流

---

## 🛠️ 课程学习要求
- 基础 Python 知识
- 可运行 Notebook 的 Python 环境
- Anthropic API Key（课程中提供申请方法）

## 学习建议
- 💻 动手编写代码：跟随视频边学边做
- ⏩ 倍速播放：加快进度节省时间，方便回顾重点
- 🧪 实际应用：尝试修改、扩展 Notebook 示例
- 🧠 遇阻就问 Claude：任何问题都可以让 Claude 协助解决

---
# 第二节：Claude 三大模型家族与选型指南

## 📌 核心观点
Claude 有 **三个模型家族**，它们功能相似，但在智能、速度和成本上各有优化重点。

---

## 📊 模型对比表

| 模型     | 智能 | 速度 | 成本 | 特点说明 |
|----------|------|------|------|----------|
| **Opus** | ⭐⭐⭐⭐ | ⭐⭐   | 💰💰💰 | 最智能，适合复杂、多步骤任务 |
| **Sonnet** | ⭐⭐⭐  | ⭐⭐⭐ | 💰💰  | 平衡型，适合大多数应用场景 |
| **Haiku** | ⭐⭐   | ⭐⭐⭐⭐ | 💰    | 最快，适合实时高频交互 |

---

## 🧠 选型指南

- 🧩 **复杂推理、优先质量** → `选择 Opus`
- ⚡ **实时响应、优先速度** → `选择 Haiku`
- ⚖️ **通用场景、均衡考虑** → `选择 Sonnet`

---

## 🔁 实战建议：组合使用模型

- `Haiku`：用户界面实时交互  
- `Sonnet`：主业务逻辑处理  
- `Opus`：需要深度智能的复杂任务

---

## 🧪 本课程使用模型
- 默认使用 **Sonnet** 模型
- 原因：智能、速度、成本三者兼顾，最适合教学和开发实践

---
# 🧠 第三课：通过 API 使用 Claude 并生成文本

---

## 🎯 课程目标

本节课将带你了解如何通过 Anthropic API 调用 Claude，并生成文本。  
我们将：
- 模拟一个基础聊天应用的后端流程；
- 讲解完整的请求生命周期；
- 探索 Claude 内部的文本生成机制。

---

## 🧱 Claude API 请求生命周期：5 个步骤

### ✅ **Step 1：前端用户发起请求**

- 用户在 Web App 中输入消息，点击“发送”；
- 消息被发送至你自己实现的后端服务器。

⚠️ **安全提示**：  
> 不要从前端（Web/Mobile）直接调用 Claude API。API 请求必须包含 Secret Key，应仅在后端调用。

---

### ✅ **Step 2：后端向 Claude API 发起请求**

- 后端接收到用户请求后，使用 Anthropic 提供的 **SDK 或 HTTP 请求**调用 API。
- 官方 SDK 支持语言包括：`Python`、`TypeScript`、`JavaScript`、`Go`、`Ruby`。

📦 必填字段：

```json
{
  "api_key": "你的密钥",
  "model": "模型名称",
  "messages": [...],
  "max_tokens": 300
}
```

---

### ✅ **Step 3：Claude 内部文本生成流程（语言模型机制）**

Claude 使用大型语言模型进行生成，过程可拆为 **四个阶段**：

1. **Tokenization（分词）**
   - 输入文本被拆解为 token（单词、部分词、符号、空格等）。
   - 示例：`"quantum"` → `["quantum"]`

2. **Embedding（向量化）**
   - 每个 token 被转换成一个 embedding（数字向量），表示其所有可能含义。

3. **Contextualization（上下文精化）**
   - Claude 根据前后 token 对每个 embedding 进行调整；
   - 使每个 token 的含义在当前上下文中唯一明确。

4. **Generation（生成）**
   - 模型预测下一个 token 的概率分布；
   - 结合概率和随机性选择下一个词；
   - 将其添加进已有的 token 列表，重复该过程直到生成终止。

---

### ✅ **Step 4：生成停止的判断机制**

Claude 每生成一个 token 都会判断是否应停止：
- 是否超过 `max_tokens` 限制；
- 是否生成了特殊的 `end_of_sequence` 停止符。

---

### ✅ **Step 5：响应返回与前端展示**

- API 返回结果至后端服务器，包含：
```json
{
  "completion": "生成的文本",
  "usage": {
    "input_tokens": 123,
    "output_tokens": 234
  },
  "stop_reason": "end_of_sequence"
}
```

- 后端将 `completion` 结果返回给前端展示在聊天窗口中。

---

## 📚 小结

- 本节目的：帮助你理解 Claude 的调用流程；
- 熟悉一些常见术语与生成机制；
- 不必死记细节，后续课程将通过实战进一步巩固。

---
# 💻 第四课：用代码调用 Claude API（入门实践）

---

## 🎯 本节目标

通过实际代码演示，完成一次最基本的 Claude API 请求。  
你将学会：

- 安装 SDK 和配置环境变量
- 创建 API 客户端
- 理解 `messages` 的结构和作用
- 发送一次完整的请求并解析 Claude 的回答

---

## 🧰 步骤 1：安装依赖

打开 Jupyter Notebook，并执行如下命令：

```python
%pip install anthropic python-dotenv
```

⚠️ VS Code 中 `%` 可能会提示语法错误，可以忽略。

---

## 🔐 步骤 2：配置 API Key

1. 在 Notebook 同级目录下创建 `.env` 文件；
2. 填入你的 API Key，格式如下：

```
ANTHROPIC_API_KEY="your_api_key_here"
```

✅ 建议把 `.env` 加入 `.gitignore`，防止密钥被上传至公共仓库。

3. 在 Notebook 中加载该环境变量：

```python
from dotenv import load_dotenv
import os

load_dotenv()
api_key = os.getenv("ANTHROPIC_API_KEY")
```

---

## 🤖 步骤 3：创建 API 客户端

```python
from anthropic import Anthropic

client = Anthropic(api_key=api_key)
model = "claude-3-sonnet-20240229"
```

---

## 📤 步骤 4：发送一次 API 请求

Claude SDK 的 `client.messages.create()` 方法接收三个关键参数：

- `model`：模型名称（如 Sonnet）
- `max_tokens`：最大生成长度限制
- `messages`：对话历史（数组格式）

### ⚠️ 关于 `max_tokens`

- 它是上限，而非目标长度；
- Claude 不会“故意”生成刚好这么长，只是遇到限制会自动终止。

---

## 📩 请求结构举例

```python
message = client.messages.create(
    model=model,
    max_tokens=1000,
    messages=[
        {
            "role": "user",
            "content": "What is quantum computing? Answer in one sentence."
        }
    ]
)
```

---

## 📝 响应处理

Claude 的返回结果结构较深，文本内容嵌套在：

```python
print(message.content[0].text)
```

只打印 Claude 返回的最终答案，忽略其他属性（如 token 使用情况、stop reason 等）。

---

## ✅ 本节总结

- 成功通过代码发送 Claude 请求；
- 熟悉 `messages` 结构：由 `user` 或 `assistant` 消息构成；
- 为后续构建对话式系统打下基础。


# 2025-07-24

<!-- Content_END -->
