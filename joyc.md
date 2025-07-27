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
# 2025-07-27

# 🧑‍🏫 第七课：使用 System Prompt 自定义 Claude 回答风格

---

## 🎯 课程目标

- 学会控制 Claude 的语气、风格和行为
- 理解并使用 `system prompt` 指导 Claude
- 支持不同场景下的角色设定（如教学辅助、客服、助手等）
- 重构 `chat()` 函数支持可选系统提示参数

---

## 🤔 使用场景：数学家教机器人

目标场景设定：

- Claude 是一位耐心的数学导师
- 学生提问如：`5x + 2 = 3 怎么解？`
- Claude 不能直接给出答案，而是要：
  - 提供提示（hints）
  - 展示相似问题的解法
  - 分步引导推理过程
- ❌ 不允许：
  - 直接给出答案
  - 建议用计算器应付

---

## ✨ System Prompt 是什么？

- 一段**字符串描述**
- 告诉 Claude 应该**扮演什么角色**、**如何回答**
- 作为参数 `system` 传入 Claude 的 `create()` 方法中

### 示例：

```python
system = '''
You are a patient and encouraging math tutor.
Never give direct answers.
Instead, guide the student step by step through reasoning.
'''
```

---

## ⚙️ Claude 的对比实验

### 🚫 无 System Prompt（默认行为）：

```python
messages = []
add_user_message(messages, "Solve 5x + 3 = 2 for x.")
response = chat(messages)
print(response)
```

Claude 会直接输出详细步骤和答案（对学习不利）。

---

### ✅ 使用 System Prompt（引导式）：

```python
response = chat(messages, system=system)
```

Claude 会改为**提示式引导**，如：

> "To isolate x, what should we do first with the constants in the equation?"

---

## 🔁 改进：将 System Prompt 变成可选参数

### 旧版本 `chat()` 缺点：

- `system` 是硬编码的，无法灵活复用
- 所有调用都固定角色

---

### ✅ 新版本：传参可选，动态组装参数

```python
def chat(messages, system=None):
    params = {
        "model": model,
        "max_tokens": 1000,
        "messages": messages
    }

    if system is not None:
        params["system"] = system

    response = client.messages.create(**params)
    return response.content[0].text
```

好处：

- 没有系统提示也能正常调用
- 想指定风格时只需加一行传参
- 更通用、更可复用

---

## 🧠 总结

| 能力 | 内容 |
|------|------|
| ✅ 控制语气风格 | 使用 `system prompt` 设定角色 |
| ✅ 提高交互性 | Claude 不再死板回答，而是引导思考 |
| ✅ 提升代码可维护性 | 动态传参，灵活控制 system 提示 |
| ✅ 实战能力提升 | 为后续构建教学助手、客服、个性化机器人打基础 |

---

下一节很可能深入**Prompt 结构设计技巧**或探讨更多系统角色模板的构造方式。

# 🧪 第八课：控制 Claude 代码输出风格（System Prompt 实战练习）

---

## 🎯 本节目标

- 学会通过 `system prompt` 控制生成代码的风格
- 尝试减少 Claude 回答中的冗余内容（注释/解释）
- 体验使用 Claude 构建“极简主义”风格的代码助手

---

## 📌 实战任务描述

> 编写一个 Python 函数：检查字符串中是否存在重复字符。

默认调用 Claude 时，它可能会：

- 输出完整函数；
- 附带解释说明；
- 添加大量注释；
- 输出格式较“啰嗦”。

---

## ✨ 提升目标：生成更简洁的实现

### 🧠 技巧：使用 System Prompt 引导 Claude

你可以通过以下 prompt 来设定 Claude 的角色：

```python
system = "You are a Python engineer who writes very concise code."
```

然后调用 `chat()` 并传入该 prompt：

```python
response = chat(messages, system=system)
```

---

## 🧾 示例结构

### 用户提问：

```python
add_user_message(messages, "Write a Python function that checks if a string has duplicate characters.")
```

### Claude 返回（简洁风格）：

```python
def has_duplicates(s):
    return len(set(s)) != len(s)
```

无需注释，无需解释，**只有核心逻辑**。

---

## ✅ 小结

| 重点 | 说明 |
|------|------|
| 🎯 控制输出风格 | 使用 system prompt 精准定义风格 |
| ✂️ 精简 Claude 输出 | 减少不必要注释/解释 |
| 🧰 Prompt 模板实用化 | 可复用于各种代码场景（工程师助手） |

---

## 💡 系统提示建议模板

你可以尝试以下风格指令：

- `You are a senior software engineer who prefers minimalism.`
- `Reply with only Python code and no explanation.`
- `Do not include any comments or print statements.`

这些将帮助你获得更加“生产环境友好”的输出。

---

📌 本节是 Claude 代码生成控制能力的初步实战，后续课程可能深入 Prompt 模板设计、行为控制与安全限制等内容。


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

# 🤖 第六课：构建循环对话的 Claude 聊天机器人（Jupyter 版）

## 🎯 课程目标：

- 在 Notebook 中运行一个简单的 Claude 聊天机器人。
- 实现输入 → 生成 → 输出 → 再输入的循环机制。
- 继续复用之前课程实现的 `chat()`、`add_user_message()` 和 `add_assistant_message()`。

## 🔄 聊天循环结构说明：

程序执行流程如下：

1. 使用 `input()` 获取用户输入。
2. 将用户输入加入消息列表（`messages`）。
3. 使用 `chat()` 调用 Claude 并获取回复。
4. 将回复加入消息列表。
5. 打印回复并回到第 1 步。
6. 重复执行，直到手动中断（如点击“停止”按钮或按 `Esc`）。

## 🧱 示例模板结构：

```python
# 初始化对话历史
messages = []

# 无限循环
while True:
    # 获取用户输入
    user_input = input(“👤 You: “)

    # 加入用户消息
    add_user_message(messages, user_input)

    # Claude 回复
    response = chat(messages)

    # 加入 Claude 回复
    add_assistant_message(messages, response)

    # 打印回复
    print(“\n🤖 Claude:\n—\n” + response + “\n—\n”)
```

## 💡 示例对话演示：

```
👤 You: What’s 1 + 1?
🤖 Claude:
—
1 + 1 equals 2.
—

👤 You: Add two to that answer.
🤖 Claude:
—
Adding 2 to the previous answer (2) gives 4.
—
```

🧠 Claude 能保留上下文的关键就在于你始终传入完整的 `messages` 列表！

—

## ✅ 本节收获：

- 掌握 `while True + input()` 搭建人机对话循环。
- 巩固了 `messages` 上下文在多轮对话中的作用。
- 拥有了一个最小可用的 Claude 聊天机器人原型。

你现在可以在本地构建、测试各种 prompt 和多轮逻辑了！

下一课可能进入系统提示设计或高级上下文管理策略。



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
