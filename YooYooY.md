---
timezone: UTC+8
---

# Wilbur

**GitHub ID:** YooYooY

**Telegram:** @Wilbur_Chan

## Self-introduction

code lover

## Notes

<!-- Content_START -->
# 2025-07-25

**Anthropic API does not store any messages or conversation history.**

> Developers must manually maintain conversation history in their code.
Sending only the latest message leads to irrelevant or off-topic responses.


helper functions simplify adding messages and sending requests:

```python
def add_user_message(messages, text):
    user_message = {"role": "user", "content": text}
    messages.append(user_message)

def add_assistant_message(messages, text):
    assistant_message = {"role": "assistant", "content": text}
    messages.append(assistant_message)

def chat(messages):
    message = client.messages.create(
        model=model,
        max_tokens=1000,
        messages=messages,
    )
    return message.content[0].text
```


maintained conversation history enables coherent multi-turn dialogues:
```python
# Start with an empty message list
messages = []

# Add the initial user question
add_user_message(messages, "Define quantum computing in one sentence")

# Get Claude's response
answer = chat(messages)

# Add Claude's response to the conversation history
add_assistant_message(messages, answer)

# Add a follow-up question
add_user_message(messages, "Write another sentence")

# Get the follow-up response with full context
final_answer = chat(messages)
```

# 2025-07-24

### Claude AI模型

Opus: 最高水平的智能，专为需要高级推理和长期规划的复杂任务设计，但成本和延迟相对较高。

Sonnet: 擅长编码和快速文本生成，适用于大多数实际应用场景。

Haiku: 最快的模型，专为响应时间至关重要的应用而优化，具有极高的速度和成本效率，**不支持推理能力**。

> 选择模型时，关键在于明确您的用例最看重什么：如果智能是首要任务，选择`Opus`；如果速度是关键，选择`Haiku`；如果需要平衡，则`Sonnet `是最佳选择。


根据应用的不同功能需求，在同一个应用中结合使用多种 Claude 模型

- Haiku 用于用户交互
- Sonnet 用于业务逻辑
- Opus 用于复杂推理。

### Claude 模型以生成文本的完整生命周期

- Request to Server
- Request to Anthropic API
- Model Processing
- Respones to Server
- Response to Client

过程：首先将用户输入分解为`Tokenization`，然后将每个token转换为包含所有可能含义的`Embed`，接着通过`Contextualization`过程根据周围的Embed调整并精确化每个Embed的含义，最后通过`Generation`阶段输出文本。

模型在生成文本时，并非简单地选择概率最高的词，而是结合了概率和随机性来选择词语，以产生更自然和多样的响应；同时，通过`Max Tokens`参数来控制生成文本的长度


### Make request

`client.messages.create()`参数解析：

```python
message = client.messages.create(
    model=model,
    max_tokens=1000,
    messages=[
        {
            "role": "user",
            "content": "What is quantum computing? Answer in one sentence"
        }
    ]
)
```

1. model(指定模型名称) - The name of the Claude model you want to use

2. max_tokens(设置模型生成文本的最大预算，作为安全机制防止生成过长文本) - A safety limit on response length (not a target)

3. messages(表示用户与模型之间的对话交流) The conversation history you're sending to Claude


<!-- Content_END -->
