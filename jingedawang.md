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
# 2025-08-01

但是这个resource和前面提到过的直接上传到Claude的resource有什么区别呢？

除了Tool和Resource，MCP server还提供了Prompt功能。MCP server可以内置一系列prompt，当用户需要某些复杂的功能时，可以直接调用预置的prompt。下面是MCP server定义的一个prompt，它可以接收用户的参数。

```python
@mcp.prompt(
    name="format",
    description="Rewrites the contents of the document in Markdown format."
)
def format_document(
    doc_id: str = Field(description="Id of the document to format")
) -> list[base.Message]:
    prompt = f"""
Your goal is to reformat a document to be written with markdown syntax.

The id of the document you need to reformat is:

{doc_id}

Add in headers, bullet points, tables, etc as necessary. Feel free to add in extra formatting.
Use the 'edit_document' tool to edit the document. After the document has been reformatted...
"""
    
    return [
        base.UserMessage(prompt)
    ]
```

和resource以及tool一样，在MCP Client端需要下面两个方法，通过session调用MCP Server的prompt。

```python
async def list_prompts(self) -> list[types.Prompt]:
    result = await self.session().list_prompts()
    return result.prompts

async def get_prompt(self, prompt_name, args: dict[str, str]):
    result = await self.session().get_prompt(prompt_name, args)
    return result.messages
```

然后我们的server可以把prompt发给Claude获取结果。

三种功能的设计思想。工具用于模型控制，资源用于应用控制，提示词用于用户控制。非常完美。当然，其实三者都需要用户实现决定可供选择的范围，只不过，最终是否使用，则是分别由模型、应用和用户决定。只有提示词用户可以决定必须用上，其它的都是由程序或者模型来决定是否调用的，这非常合理。

![image.png](attachment:bc620362-3453-4410-8c6c-580837600ae6:image.png)

# 2025-07-31

MCP其实是Claude原本的tool use功能的增强。在前面的介绍中，我们已经知道，要想让Claude调用tool，需要写好tool schema，并实现tool的功能。如果我们想要实现一些和第三方服务相关的功能，比如，与GitHub访问有关的功能，我们就得在我们的tool的实现中调用GitHub API。这很麻烦，而且这种事情是可以复用的，完全可以由一拨人专门写这种tool，然后让其他人直接调用。于是这个需求就诞生了MCP。它就是鼓励服务的提供商自己实现MCP接口，提供tool给其他用户。调用Claude的用户只需要引入这些别人制作好的tool就行了。这和直接引入Claude内置的tool效果应该差不多。

接下来，我们看看整个调用MCP的流程是怎么进行的。首先，用户给我们的服务发prompt。我们借助内置的MCP Client向MCP Server发出ListToolsRequest请求，获取到支持的tool列表。这一步其实替代了之前我们自己维护的tool list。接下来，我们把用户query和tools发送给Claude，Claude经过分析，认为应该调用某个tool，于是返回tooluse request。我们收到这个tooluse request后，直接转发给MCP Server。MCP Server在内部会调用Github API获得所需的内容，然后以CallToolResult接口返回。我们再把返回的结果交给Claude，由Claude最终整合所有信息，给出回答。整个过程中，MCP起到的作用就是替代我们维护的tool list，以及替代我们实现所有的tool。

![image.png](attachment:53fff6e9-b6b1-44ff-8ed0-1415e3a321b9:image.png)

通常，我们只需要实现MCP Server（如果我们是提供服务的一方）或者实现MCP Client（如果我们是与用户交互的一方）。但接下来我们会同时实现Server和Client，以搞清楚MCP的原理。

先看MCP Server这边。使用MCP SDK可以非常方便地利用decorator @mcp.tool来实现tool，而不需要写JSON schema。

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("DocumentMCP", log_level="ERROR")

docs = {
    "deposition.md": "This deposition covers the testimony of Angela Smith, P.E.",
    "report.pdf": "The report details the state of a 20m condenser tower.",
    "financials.docx": "These financials outline the project's budget and expenditure",
    "outlook.pdf": "This document presents the projected future performance of the",
    "plan.md": "The plan outlines the steps for the project's implementation.",
    "spec.txt": "These specifications define the technical requirements for the equipment"
}

@mcp.tool(
    name="read_doc_contents",
    description="Read the contents of a document and return it as a string."
)
def read_document(
    doc_id: str = Field(description="Id of the document to read")
):
    if doc_id not in docs:
        raise ValueError(f"Doc with id {doc_id} not found")
    
    return docs[doc_id]
```

可以`mcp dev mcp_server.py`启动一个MCP inspector，用来调试实现的tool能否正常工作。这可以在Client Server联调之前解决Server端的问题。

![image.png](attachment:17f715be-0ad7-4ac7-8fd9-95acdb991fb2:image.png)

Client端的实现更简单，只需要调用Session类的list_tools方法和call_tool方法即可。

```python
async def list_tools(self) -> list[types.Tool]:
    result = await self.session().list_tools()
    return result.tools

async def call_tool(
    self, tool_name: str, tool_input: dict
) -> types.CallToolResult | None:
    return await self.session().call_tool(tool_name, tool_input)
```

Server端除了tool，还提供了resource功能。你可以把文档、图片、JSON等等资源作为resource提供给客户端。实现的方法也很简单，还是用decorator就行。没有参数的resource提供静态资源，一般是resource list。有参数的成为resource template，可以根据id返回对应的resource。resource也可以用inspector调试。

# 2025-07-30

Claude的reasoning不需要切换模型，而是在同一个模型里面打开thinking模式即可。只需要设置额外参数think=True和think_budget。这种模式叫做extended thinking。

thinking模式下会返回thinking block和text block。其中，thinking block可能是明文，也可能是密文。当Claude的内部审查机制认为这段reasoning不适宜公开时，它会返回密文。之所以一定会返回，是为了方便用户把thinking也加入history，组成多轮对话。

为了避免用户在多轮对话的情况下篡改thinking内容，thinking block里面添加了一个signature，我猜是用thinking text+某些密钥生成的签名，只有当history中的内容能和signature对的上的情况下，Claude才会处理请求。

Claude支持图片，但对大小、尺寸都有限制。图片的像素会转换成token，每个token代表750像素。

用户发送的请求可以同时包含一个image block和一个text block。

处理图像的prompt也可以写得复杂一些，可以使用之前提到过的prompt技巧，比如规定处理步骤、few-shot等等。

Claude支持PDF，且可以读取PDF中的文本、结构、图片、图表、表格等等数据。

Claude支持Citation，对于所有文档类型的附件，都可以开启citation，从而在返回文本中标注引用来源。当然，这个配合上前端显示效果更好。

Prompt Caching支持缓存prompt，这样在之后使用相同的prompt前缀的时候就可以复用之前计算过的结果，以节约成本。但注意，缓存只保留一小时。

Cache需要手动开启，而且需要指定缓存到哪个block。因为Claude并不会选择每次都缓存完整的context，那样的话cache也太大了。所以，由用户指定缓存到哪里，用户比较清楚那些部分是不变的，因而需要缓存。

在user message里面加缓存需要稍微改动一下格式。从context的开头到指定的block，整个过程被记录为一个cache。只要新的request从开头开始与这个cache完全重合，就可以利用到这个cache。

![image.png](attachment:5b7f23c9-7d0d-495b-98d2-3c47f164b132:image.png)

除了message可以加cache，tool、image、system message都可以加cache。甚至可以加多个cache。Claude会按照如下顺序处理tool、system prompt、user message、assistant message。这里的加的两个cache各自独立存在。如果新的request在第一个user message处变了，它会使用tool#3的cache。如果新的request在第二个user message处变了，它会使用assistant message的cache。

![image.png](attachment:b1b962b1-a561-4702-ad02-0c51f055fe9d:image.png)

被cache的context长度必须大于1024。

通常，system prompt和tool list都是固定的，所以在这两个地方添加cache是常规做法。

实际实施的时候，需要注意，给tool list的最后一个tool添加cache control需要小心副作用。如果我们给一个tool加了cache control，然后我们又偶然修改了tool list的顺序，那样的话我们就有可能给多个tool添加cache control，从而生成多个cache。这是没必要的。

前面提到的Image、document等message都是在单次request中随user message一并发出的。但还有一种做法是单独调用一个file upload API，把文件传上去，Claude会返回一个file id。这个id可以在后面任意的request中复用。

Claude有一个内置的code execution tool，我们只需要在tool list中加入它的id即可开启。如果同时把file id放在一个file block里面随user message发起请求，Claude就能选择编写代码处理那个文件，并选择生成新的文件，并返回生成文件的id。用户可以根据id下载那个文件。这个功能非常适合用来处理文档数据并绘制图表。其实官方的Claude App都实现了这些功能。说明只要有了API，我们也可以自己实现官方提供的功能。

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
