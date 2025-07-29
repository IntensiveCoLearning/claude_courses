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
# 2025-07-29

介绍RAG技术。

文本切块text chunking可以有以下几种：

- Size based chunking
- Structure based chunking
- Sentence based chunking

![image.png](attachment:62f5e9b3-5ff0-4cf0-9f4e-21f564c0195f:image.png)

![image.png](attachment:46ec449b-4651-4576-893b-a0b88eb6f6f4:image.png)

只用semantic search（embedding）并不一定每次都好用，有些冷门术语、唯一标识符等文本并不能被很好地理解，所以需要结合lexical search，比如BM25。可以同时使用embedding search和BM25 search，把两个结果合并。

如果用多个index，可以用以下方法把每个index的ranking merge到一起

![image.png](attachment:32ba1fee-b820-4572-b46f-26d8de0b2fce:image.png)

如果这个结果还是不让人满意，还可以在后面增加一个reranking阶段，使用Claude来做rerank，结果就会更好。相当于前面的index用作retrieval，后面的reranking用作排序。

chunking还有一个问题是缺乏上下文，有些时候单独的一小段话可能会有歧义。此时使用contextual retrieval技术，把整个document和chunk拿给Claude，让Claude总结以下这个chunk在整个文档中所处的地位，相当于总结以下chunk的上下文。这样等后面retrieve到这个chunk的时候，就知道这个chunk的上下文大概是什么了。这个技术倒是第一次听说，不知道有没有人用，好像成本有点高。

# 2025-07-28

Claude有可能会在接到tooluse result之后，发现还需要再调用另一个工具，于是再次发起tooluse request。所以我们需要再次处理这个tool use请求。这就是多回合的工具使用，需要支持这种情况。

![image.png](attachment:51faebc4-f79b-4ccf-9dd1-6dbd54dfebc1:image.png)

虽然Claude可以一次性request多个tool use，但这不是必须的，有时候Claude会先request一个，等结果返回后再request另一个，即便这两个互不相关。为了鼓励Claude一次性把能调用的tool都调用到，我们可以提供一个batch_tool工具。这个工具是个meta tool，它的参数是想要调用的tool列表和调用参数。于是，当Claude想要调用多个工具时，它可以直接调用这个batch_tool，把那些工具和参数传进去，这样更快捷方便。但我不知道Anthropic是否针对这种情况做了训练，否则Claude为何会优先调用这个batch_tool而不是同时直接调用多个tool呢？

调用Claude API的时候其实有一个参数可以控制Claude强制调用某个tool。这可以用来获得结构化的输出。前面讲过，可以用prompt prefilling和stop sequence来格式化输出，但那个还不够严格。我们可以写一个tool，把参数设计成我们想要格式化的格式，然后让Claude按照这个格式调用这个tool。我们只需要把request中的参数取出来直接用就行了，不需要返回tool response。不过，Claude会不会在等response呢？

![image.png](attachment:cba76520-3a26-4010-9143-68220b85a466:image.png)

在stream模式下，tool use的request并不像text一样以chunk返回，或者说，虽然以chunk返回，但API会先做一次buffer和validation，每次top level key-value pair完整后才返回。如果想要立即返回每个chunk，可以开启fine-grain模式。

Claude内置了一个text editor tool，这个tool的schema是官方写好的，所以我们不需要定义，只需要根据名字引入即可。但注意，它仍然只是一个tool，也就是说，Claude决定调用这个tool的话，会发送tool use request，我们接收到这个request后需要实现处理逻辑。比如，Claude决定打开某个文件、写入某一行文本，这些操作其实是需要我们来做的，Claude只是决定做哪些事而已。它可以做的事情如下。

![image.png](attachment:b8d57636-1dfb-4a51-bcb9-907c6a0f0a6a:image.png)

Web search工具也是内置的，而且既不需要用户写schema，也不需要用户写implementation。Claude会调用web search tool，并根据结果来决定采用那些内容辅助后续回答。当然，这个tool的schema可以让我们更详细地看到它提供的信息格式。

# 2025-07-27

给Claude发请求的时候带上支持的function list，就激活了tool use能力

```python
messages = []
messages.append({
    "role": "user",
    "content": "What is the exact time, formatted as HH:MM:SS?"
})

response = client.messages.create(
    model=model,
    max_tokens=1000,
    messages=messages,
    tools=[get_current_datetime_schema],
)
```

如果Claude决定调用某个tool，它会在回复中包含两个block，包括一个常规text block和一个额外的tooluse block。

![image.png](attachment:83ede2fd-8169-4d88-a0d6-8ce2871c621e:image.png)

然后调用方解析出tool use block的内容，调用该tool。注意，这表明调用tool是调用方的责任，Claude只是负责帮你决定是否调用tool以及调用哪个tool，它自己并不直接调用tool，由客户端负责调用。所以，如果是Claude web app，里面内置了调用tool的service。但如果是我们自己调用Claude API，那显然我们就要自己实现调用tool的逻辑。

这里说的两个block其实是在一个content里面，结构类似

```python
{
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "我将为您查询天气信息。"
    },
    {
      "type": "tool_use",
      "id": "toolu_01Abc123",
      "name": "get_weather",
      "input": {
        "location": "Beijing"
      }
    }
  ]
}
```

我们自己执行tool function，得到结果后，再发送一个user request给Claude，里面带上tool function的结果，格式为

```python
messages.append({
    "role": "user",
    "content": [{
        "type": "tool_result",
        "tool_use_id": response.content[1].id,
        "content": "15:04:22",
        "is_error": False
    }]
})
```

整个流程如下。注意，在给Claude发送tool function的结果时，需要包含完整的history，需要带上tool schema。

![image.png](attachment:0d7e17df-91da-4fa1-b858-a1c21a4c6b3c:image.png)

有时候，Claude可能会决定一次性调用多次tool，我们就需要把每次调用的结果都返回回去。

# 2025-07-26

Claude如何调用工具？

首先，有些任务需要调用工具才能完美解决。比如，设置一个10天后提醒的闹钟。

我们需要实现一些工具函数，比如get_weather。实现的代码怎么写都行，关键是要写一个JSON Schema，把函数的功能和调用方式告诉Claude。

![image.png](attachment:7c684d43-c883-48f9-977b-cf93999dd1a8:image.png)

写JSON Schema的一个捷径是让Claude来写。把代码告诉Claude，然后把Anthropic官方的JSON Schema文档也告诉Claude，然后说："Write a valid JSON schema spec for the purposes of tool calling for this function. Follow the best practices listed in the attached documentation.”

# 2025-07-25

Prompt Evaluation是Prompt Engineering有效的保证。调整prompt需要确保它在许多case上表现良好，所以需要一个evaluation pipeline。一般情况下，大项目都需要有这样的流程。当然，为了方便，也可以不做evaluation，随便测几个case就用，只适合小项目，且不计较效果的情况。

用LLM生成Test Dataset，虽然方便，但我感觉这样会显得有点不靠谱。此外，用LLM生成dataset的话，需要同时生成grade criteria，以方面后面用AI评分。

Evalution的一个难点是如何grade。可以用固定规则、AI或人工。能用规则的肯定尽量用规则，用不了的就用AI。要求更高的话就用人工。涉及到代码生成的，可以用规则来验证代码质量，就像IDE做的那样。所以代码的评估应该是最成熟的。用AI评分的时候，除了分数，最好让AI给出strength、weakness、reason，这样方便我们基于这些解释修改prompt。

这道题的答案我不太认同。我觉得应该选A，应该用最好的模型而不是最差的模型。不然生成的dataset根本没什么价值。当然，如果这里说的test case只是用了简单测试一下能不能跑通的话，那就没问题。

> You need test cases for your prompt evaluation. You have two options: write them by hand or use Claude to generate them. Which model should you use for generation?
> 
> 
> ~~The most expensive model available~~
> 
> ~~Multiple models combined~~
> 
> **A faster model like Haiku**
> 
> ~~The same model you're testing~~
> 

下面是一系列prompt engineering方法：

- Be clear and direct。问具体的问题和要求，不要含糊其辞。
- 给出guideline，指出质量上的要求
- 要求按照规定的步骤回答
- 如果长度比较长，建议用XML标签区分不同部分，以免LLM搞不清楚。XML是LLM识别最准确的格式。
- 使用examples，one-shot或few-shot

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
