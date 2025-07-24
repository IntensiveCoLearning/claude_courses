---
timezone: UTC+8
---

# 王金戈

**GitHub ID:** jingedawang

**Telegram:** @Jinge

## Self-introduction

热爱学习和分享的 AI 探索者

## Notes

<!-- Content_START -->
# 2025-07-24

## **Introduction**

## **Anthropic overview**

介绍了大杯Opus、中杯Sonnet和小杯Haiku的区别及适用场合。

## **Accessing Claude with the API**

这个5步流程还是挺清晰的。特别提到，不要在我们自己的开发的客户端里面直接调用LLM API，因为那样的话会不得不把API key放在客户端，这非常危险。所以，最好的做法是自己做一个服务端，自己的客户端只调用自己的服务的，在服务端调用LLM API。

![image.png](attachment:9504d6e4-8bb9-4d15-bf3c-0f3286828766:image.png)

还用非常简练的方式解释了LLM的工作原理，这些图可以用作参考。

![image.png](attachment:8c206329-4004-409c-b19f-d29b333353eb:image.png)

用Python调用Anthropic API时，只需要把**`ANTHROPIC_API_KEY="your-api-key-here"`**写入.env文件，即可通过认证。

只支持两种role：user和assistant。没有system吗？

多轮对话需要开发者自行管理对话历史，这一点对于初学者来说至关重要，会打破许多人的认知。

![image.png](attachment:4a70fa3c-1769-42b1-8f0b-8872b8b92e12:image.png)

做了一个小练习，让读者自己实现一下多轮对话的功能，直接在命令行中读取用户输入。

Anthropic也支持system prompt，但不是作为messages的role，而是作为一个单独的参数。这和其它LLM API不太一样，可能Anthropic想要提升system prompt的地位吧，像DeepSeek已经抛弃system prompt了。

讲解温度的作用和实现方法：

![image.png](attachment:c639276b-e9cb-4108-9709-310ece3a61c3:image.png)

![image.png](attachment:af29a07a-0dd5-4dab-9e0f-0f9f1484c764:image.png)

不同温度适用的范围：

![image.png](attachment:53e31976-aff2-4c2a-851c-241827cf8ca8:image.png)

介绍了stream的用法和作用。每条数据可能有很多类型，不过实际用的时候不需要管，直接stream.text_stream就行了。

![image.png](attachment:189d63f1-ac05-4728-9999-97fb69177cfb:image.png)

![image.png](attachment:dc14bcad-c429-444d-b1b5-db045592adc4:image.png)

Anthropic支持prefill assistant message，这个很有用，可以强制要求AI的回复以某段话开头。这在jailbreak的时候估计也有用。它也可以用来生成biased response，因为我们可以提前固定回复的态度和语气。不过，按理说我们也可以在user message里面要求Claude以某段话为开头继续答复，只不过那样不够自然而已，不知道实际操作的时候有多大区别。

![image.png](attachment:9fbfedbb-c218-4780-a1ae-8e961276dbdf:image.png)

如果想要JSON格式，OpenAI提供了一个参数，直接确保回复是json字符串。但Anthropic似乎没有，它们给出的方案是用prefilling和stop sequence来解决这个问题。当然，这样也挺方便的，就是不够优雅。

```python
messages = []

add_user_message(messages, "Generate a very short event bridge rule as json")
add_assistant_message(messages, "```json")

text = chat(messages, stop_sequences=["```"])
```

# **Anthropic overview**

介绍了大杯Opus、中杯Sonnet和小杯Haiku的区别及适用场合。

# **Accessing Claude with the API**

这个5步流程还是挺清晰的。特别提到，不要在我们自己的开发的客户端里面直接调用LLM API，因为那样的话会不得不把API key放在客户端，这非常危险。所以，最好的做法是自己做一个服务端，自己的客户端只调用自己的服务的，在服务端调用LLM API。

![image.png](attachment:9504d6e4-8bb9-4d15-bf3c-0f3286828766:image.png)

还用非常简练的方式解释了LLM的工作原理，这些图可以用作参考。

![image.png](attachment:8c206329-4004-409c-b19f-d29b333353eb:image.png)

用Python调用Anthropic API时，只需要把**`ANTHROPIC_API_KEY="your-api-key-here"`**写入.env文件，即可通过认证。

只支持两种role：user和assistant。没有system吗？

多轮对话需要开发者自行管理对话历史，这一点对于初学者来说至关重要，会打破许多人的认知。

![image.png](attachment:4a70fa3c-1769-42b1-8f0b-8872b8b92e12:image.png)

做了一个小练习，让读者自己实现一下多轮对话的功能，直接在命令行中读取用户输入。

Anthropic也支持system prompt，但不是作为messages的role，而是作为一个单独的参数。这和其它LLM API不太一样，可能Anthropic想要提升system prompt的地位吧，像DeepSeek已经抛弃system prompt了。

讲解温度的作用和实现方法：

![image.png](attachment:c639276b-e9cb-4108-9709-310ece3a61c3:image.png)

![image.png](attachment:af29a07a-0dd5-4dab-9e0f-0f9f1484c764:image.png)

不同温度适用的范围：

![image.png](attachment:53e31976-aff2-4c2a-851c-241827cf8ca8:image.png)

介绍了stream的用法和作用。每条数据可能有很多类型，不过实际用的时候不需要管，直接stream.text_stream就行了。

![image.png](attachment:189d63f1-ac05-4728-9999-97fb69177cfb:image.png)

![image.png](attachment:dc14bcad-c429-444d-b1b5-db045592adc4:image.png)

Anthropic支持prefill assistant message，这个很有用，可以强制要求AI的回复以某段话开头。这在jailbreak的时候估计也有用。它也可以用来生成biased response，因为我们可以提前固定回复的态度和语气。不过，按理说我们也可以在user message里面要求Claude以某段话为开头继续答复，只不过那样不够自然而已，不知道实际操作的时候有多大区别。

![image.png](attachment:9fbfedbb-c218-4780-a1ae-8e961276dbdf:image.png)

如果想要JSON格式，OpenAI提供了一个参数，直接确保回复是json字符串。但Anthropic似乎没有，它们给出的方案是用prefilling和stop sequence来解决这个问题。当然，这样也挺方便的，就是不够优雅。

```python
messages = []

add_user_message(messages, "Generate a very short event bridge rule as json")
add_assistant_message(messages, "```json")

text = chat(messages, stop_sequences=["```"])
```


<!-- Content_END -->
