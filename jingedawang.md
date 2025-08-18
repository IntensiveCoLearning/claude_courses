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
# 2025-08-18

Model Context Protocol: Advanced Topics这门课也学完了。关于Sampling、Roots、StreamableHTTP的内容还是比较有用的。

# 2025-08-17

Server to client request or notification需要SSE，但在多server的load balance的场景下，这种功能不太好搞。因为MCP Client可能和第一个MCP Server建立SSE连接，但它的下一次tool call有可能被分发到另一个MCP Server。而在tool call中调用的progress notification则需要走第一个SSE连接。MCP的协议里面没有实现这么复杂的通信功能，当然，我们开发基础设施的时候可以实现这些功能，但非常麻烦。所以简单起见，可以disable掉SSE的功能，即禁止server to client的消息。

![image.png](attachment:92a27906-27e6-48b2-949d-2e84af9c76c2:image.png)

这可以通过设置下面两个参数实现。stateless_http=True会禁用session id，于是整个过程变成了无状态的，此时client不需要三次握手初始化，也不需要发送GET SSE连接，progress notification被禁用。json_response=True会禁用每次server response的SSE短连接，于是server的每次response都返回完整的JSON，而不是以stream的方式返回。

![image.png](attachment:9c2e6a37-c49d-41be-98f3-c189cd70f785:image.png)

今天看到一篇文章说MCP协议设计的过于简单，缺乏remote call所需的安全机制。从目前学到的这些设计来看确实比较粗糙，不过感觉这些可能都只是临时方案，以后用的人多了自然会不断改进。

# 2025-08-16

StreamableHTTP其实是用了SSE（Server-Sent  Events）技术。当Client向Server建立SSE连接时，这个连接的有效时间无限长，Server可以在任意时刻向Client发送消息。

所以整个流程会变成这样。仍然是先三次握手，但Server的第一次返回会带上一个mcp-session-id，后面的request必须带同一个id，才会被当作同一个session。三次握手后，客户端必须主动发出一个GET请求，然后Server端返回SSE response作为长连接。该连接保证了server端可以随时向客户端发起请求。如果client打算调用tool，仍然是发出call tool request，server的response仍然是SSE形式的，但这个tool result response在返回完会自动关闭。从server端返回的progress notification会走前面的长连接。而从server端返回的information notification则会走后面的短连接。至于为何如此，下节应该会讲。此外，这个Get请求是MCP Client实现的时候手动发还是自动包含在MCP框架里面呢？不太清楚。

根据Kimi的解释，这个GET请求是MCP SDK自动发的，调用client.connect()的时候会自动完成三次握手和GET请求。progress走长连接的SSE通道是因为progress一般是跨tool call的进度，所以没法放在短连接的SSE里面，而info则是属于一次tool call的信息。

# 2025-08-15

由于HTTP server没法主动向HTTP client发请求，所以MCP中的4种通信方式受限于传统的HTTP协议。包括Create message request, list roots request, progress notification和logging notification。

![image.png](attachment:5a052842-d4e2-40fe-9e98-07b1f727959d:image.png)

不过，MCP提供了streamablehttp来解决这个问题。

# 2025-08-13

MCP内部使用JSON格式的字符串作为传输协议，规定了两种类型的消息。一种是Request-Result消息，由Client发出request，由Server返回Result。另一种是Notification消息，双方都可以发出，不需要接收。上节介绍的progress和logging就属于这类，它们由server发出client接收。Initialized和Canceled notification则由client发出server接收。这个消息分类与后面介绍的传输协议有关。

![image.png](attachment:c236099d-80d3-4da9-b69d-3cd146166109:image.png)

MCP建立连接需要经过三次握手，分别是Client发出初始化请求，Server返回初始化结果，Client再发出初始化提醒。接下来才可以正常运行。

![image.png](attachment:87e3c6b9-d25d-45d6-9eb8-240faa73aad6:image.png)

第一个传输机制是stdio，当client和server位于同一台机器上时，让server作为client的子进程启动，client就可以通过调用server的stdin输入消息，然后server的stdout输出消息，完成通信。

![image.png](attachment:599ed8d1-d863-4e83-9abc-556e8d04070a:image.png)

stdio通信的好处是这个通道对双方是对等的，client可以主动发出请求，server也可以。但HTTP通信则不行。所以stdio通信支持了MCP协议的完整功能，但后续介绍的HTTP则不能实现完整的双向通信机制。

![image.png](attachment:37704673-750a-4126-8386-7712f39521c7:image.png)

# 2025-08-12

MCP提供了log和progress功能。

MCP Server可以直接调用context.info和context.report_progress方法向Client发送log和progress。

![image.png](attachment:15f90d06-34f6-4643-a8f8-9ff9f223f300:image.png)

MCP Client则预先创建log callback和progress callback以接收这些信息。至于如何显示，这里没实现，取决于你的应用怎么设计。

![image.png](attachment:3d5f3106-8dce-4fff-879b-041e4ac6995c:image.png)

Roots是Anthropic建议的一个功能。在任何访问用户本地文件系统的工具中，都建议先检查目标文件是否在Root list规定的目录中。不在的话就拒绝访问文件，这样比较安全。不过，这只是一个best practice，不是MCP内置的规定。如果实现的话，可以添加list_roots工具和read_dir工具来从roots目录下查找用户请求的文件。不过，Root这个类型是mcp.types里面内置的。只是这个协议没内置而已。

![image.png](attachment:cf7dc0ad-0a65-4ea3-a075-d210bdbc3315:image.png)

MCP Client端需要实现一个list_roots callback，然后传递给mcp.ClientSession的list_roots_callback参数。这样，在MCP Server里面可以直接通过调用context.session.list_roots方法拿到这个callback的返回值。一般情况下，我们在MCP Server里面创建一个同名的list_roots tool，在内部拿到roots。

但需要注意，这个demo是一个简化的例子，MCP Server和MCP Client运行在同一个机器上，使用stdio通信，所以MCP Server可以直接访问用户授权的本地文件。实际场景下，如果server和client不在一起，就得通过其它方式访问用户的文件，比如MCP Resource。但这感觉还是很乱，可能需要接触到更复杂的项目才能搞清楚。

# 2025-08-11

MCP的高级概念，第一个：Sampling。

这个以前其实学到过，就是当MCP Server在执行任务的时候，如果需要调用AI，可以申请调用MCP Client端的AI。这个设定猛一看有点奇怪，感觉很混乱，但仔细想想却很有道理。比如，我提供一个MCP Server，我本身已经提供服务了，如果我还要负担这个过程中的AI调用的话，我的成本就太高了。我可以把这个成本转移到用户上，他们本来就在用AI，所以他们并不在意多用一次AI。所以，我只要定义清楚我会在某些功能中反向调用AI，用户确认了就好。

这个功能的实现需要在MCP Server和MCP Client各自实现一部分代码。在Server端，我们直接调用context的create_message方法发送请求。

```jsx
@mcp.tool()
async def summarize(text_to_summarize: str, ctx: Context):
    prompt = f"""
    Please summarize the following text:
    {text_to_summarize}
    """
    
    result = await ctx.session.create_message(
        messages=[
            SamplingMessage(
                role="user",
                content=TextContent(
                    type="text",
                    text=prompt
                )
            )
        ],
        max_tokens=4000,
        system_prompt="You are a helpful research assistant",
    )
    
    if result.content.type == "text":
        return result.content.text
    else:
        raise ValueError("Sampling failed")
```

在Client端，我们需要实现一个回调函数，当收到来自Server端的请求时，该回调函数被调用，我们在该函数中发起真正的AI调用。在创建ClientSession的时候，把回调函数作为参数传进去。

```jsx
async def sampling_callback(
    context: RequestContext, params: CreateMessageRequestParams
):
    # Call Claude using the Anthropic SDK
    text = await chat(params.messages)
    
    return CreateMessageResult(
        role="assistant",
        model=model,
        content=TextContent(type="text", text=text),
    )

async with ClientSession(
    read,
    write,
    sampling_callback=sampling_callback
) as session:
    await session.initialize()
```

整个流程长这样

# 2025-08-09

Deep dive into MCP's advanced features including sampling, notifications, and transport implementations

这门课深入介绍MCP，感觉可以学一下。

# 2025-08-08

学完了Claude with the Anthropic API这个课程，还是挺全面的，把prompt engineering、RAG、tool use、MCP、Claude Code都讲到了。深入程度恰到好处，适合开发者学习。

# 2025-08-06

Route pattern适合于先分类，然后适用特定类别的prompt来处理的任务。

![image.png](attachment:7ae88d90-aca5-4b71-b2d3-dc464a342783:image.png)

Agent和Workflow的差别就在于Agent把解决任务的过程交给模型来决定，模型可以非常灵活地设计不同的解决方案。Agent本质上就是LLM+Tools，之所以有效，是因为LLM可以有创造性地把tool use以最合理的方式串起来。下面是一个具体的例子，三个tool的不同组合可以用来解决非常多不同种类的问题。提供给LLM的tool应该尽量抽象，不要太局限。

![image.png](attachment:86a0d92a-f92c-4703-9d06-b3e0f0dc97eb:image.png)

一定要让Claude能够随时获取到它所处的环境状态，无论是执行操作前还是执行操作后。提供环境反馈才能让Claude更好地完成任务，并动态调节自己的下步动作。

# 2025-08-05

Workflow和Agent的区别是，前者由开发者预定义一系列操作，整个流程是预先确定的。而后者则完全由AI自行决定。

Workflow在实践中用的很多，因为大部分业务都有明确的最佳流程。比如下面这个例子，输入一张图片，生成CAD模型。我们可以设计一个evaluator-optimizer的工作流，让claude观察图片后生成CAD代码，渲染后再让Claude检查和原图的差别，然后反复修改，直到Claude认为生成的模型很好地还原了图片。这是一个常用的workflow pattern。

![image.png](attachment:9a501438-fa11-46a8-87e2-2ea9ebe4dc68:image.png)

Workflow pattern还有很多。下面是并行workflow pattern。我们可以把多分类问题转化成多个二分类问题，从而让每个prompt变得更专一，或许可以提高Claude的正确率，而且并行化还能提高效率。不过cost应该会增加。还有一个好处是并行化后可扩展性更强，增加一种选项只需要增加一个subtask，而不需要改一个巨大的prompt。并行化pattern的适用场景是，当我们需要在一个prompt里面写多个标准时，就意味着我们可能需要把它们拆开到多个prompt里面。

![image.png](attachment:07fca7fa-a6f7-49fe-861f-f6aae970cf69:image.png)

![image.png](attachment:5021bd0e-32b2-4dd9-bf48-87e41a86fc1a:image.png)

![image.png](attachment:39ecd3af-af31-420d-b382-336a82721425:image.png)

下一个workflow pattern是chaining。当一个任务可以拆分成多个前后顺序执行的任务时，把多个步骤串起来，并可以在两个任务之间执行non-LLM的操作。

![image.png](attachment:ec93f9d5-7c8e-4132-bb6a-d5754e2d3706:image.png)

一个典型的例子是，如果你让Claude写一篇文章，同时提了很多限制，它可能会写出来，但不满足某些限制。无论你在prompt里面写得多清晰，它还是有可能不遵守你的命令。这是因为prompt太长了，要求太多，导致Claude注意力难以集中到每个要求上。这也是LLM的通病，虽然大家在慢慢改善，但显然现在还没法完全解决这个问题。所以，一个现实的方法是，把写出来的文章再让Claude根据那些要求改一遍。在改的时候，Claude可以专注于这些要求，而不需要同时思考如何创作。本质上，让LLM一次只做一件事它会做得更好。

# 2025-08-04

另一个best practice是，用Claude Code自动debug线上error。可以在GitHub上创建一个action，每天自动拉取线上服务的log，从log中检查看有没有error，有error的话就修复，并自动提交PR。整个过程由Claude Code自动完成，我们只需要review最后的PR并通过即可。

Claude Code还可以用来做前端的自动化测试。让Claude自动测试一个网页的各项功能是否正常，它可以采取屏幕操作，并观察现象，得出结论。下面是一个具体的prompt

```
Your goal is to conduct QA testing on a React component hosted at https://test-mentioner.vercel.app/

Testing process:
1. Open a new browser tab
2. Navigate to https://test-mentioner.vercel.app/
3. Execute the test cases below one by one
4. After completing all tests, write a concise report

Test cases:
1. Typing 'Did you read @' should display autocomplete options
2. Typing 'Did you read @' then pressing enter should add '@document.pdf'
3. After adding '@document.pdf', pressing backspace should show autocomplete options directly below the text, not elsewhere on the page
```

Computer Use功能会在Docker环境中运行，以避免对真实环境造成任何影响。

Computer Use本质上就是tool use，这个tool是computer action，应该是Claude内置的tool，可以直接引入。这个tool提供了许多GUI操作，比如移动鼠标、点击、屏幕截图等等。Claude运用这些操作来完成computer use的任务。不过，显然Claude需要连续调用多次action才能完成一次任务，不过视频里没有详细讲整个过程。为了启动这个tool，还需要我们自己装docker。

# 2025-08-03

Claude Code内置了MCP Client，所以可以直接添加MCP Server，它就会在适当的时候调用工具。

如果开发的feature比较耗费时间，我们也可以并行开多个claude code同时开发多个features。这可以使用Git Worktree功能，把多个分支的代码同时checkout到一个子目录里面。Git支持这个功能，我之前不知道，遇到这种情况总是clone多份分别开发，原来可以直接在一个目录下解决这个问题。在每个worktree目录内部，启动一个新的vscode，启动Claude Code，开发新feature。这个做法其实是一个best practice，并不是Claude Code原生支持的功能。因为创建worktree并启动新的vscode是需要我们手动写prompt来实现的。虽然这个prompt可以放在commands目录下的md文件中，作为/xxcommand快捷调用，但还是要自己写prompt模板。所以，本质上，这只是告诉我们当我们想用Claude组件一个开发团队的话，可以这样干。

![image.png](attachment:d7035616-6ec8-469a-a833-ed4c2d47c061:image.png)

[]()

# 2025-08-02

Claude Code似乎是基于NodeJS开发的，需要用npm安装。

```bash
npm install -g @anthropic-ai/claude-code
```

Claude是一个全流程开发工具，可以把它当作一个软件工程师，它可以做下面所有的事情

![image.png](attachment:3c5487aa-2a61-4a1a-bb34-e5c0ceef75cb:image.png)

使用/init命令可以生成一个CLAUDE.md文件，Claude会在其中总结整个project的概况，为后续所有对话提供上下文。这个文件可以是project scope、local scope或user scope。默认是project scope。用#开头的命令可以往CLAUDE.md里面添加内容。比如 **`# Always use descriptive variable names` 。**

![image.png](attachment:510d7c50-f17d-4ad7-ae4a-430b106a8f00:image.png)

使用Claude Code开发新feature有两种方式可以采用。

第一种就是传统的先分析需求、设计方案、实现、测试。

![image.png](attachment:072e916f-7d56-457a-ae38-045db4891388:image.png)

第二种是测试驱动，先分析需求，然后设计测试，实现测试，最后实现代码并通过测试。

具体做法就是在terminal里面和Claude Code对话，按照三个步骤或四个步骤逐步要求它做。

```bash
// First, ask Claude to read relevant files
> Read the math.py and document.py files

// Then ask for planning (not implementation)
> Plan to implement document_path_to_markdown tool:
1. Create a function that:
   - Takes a file path parameter
   - Validates the file exists  
   - Determines file type from extension
   - Reads binary data from file
   - Leverages existing binary_document_to_markdown function
   - Returns markdown string
2. Add appropriate documentation
3. Register the tool with MCP server
4. Add tests

// Finally, ask for implementation
> Implement the plan
```

Claude Code可以完成git操作，可以自动修复bug。反正把它当作一个工程师就行了，人类工程师能做的它都能做。

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
