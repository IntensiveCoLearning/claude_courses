---
timezone: UTC+12
---

# Bruce Xu

**GitHub ID:** brucexu-eth

**Telegram:** @brucexu_eth

## Self-introduction

在看 AI 的 E 卫兵

## Notes

<!-- Content_START -->
# 2025-08-22

# Anthropic apps

Computer Use - Explore this set of tools that lets Claude interact with desktop applications

Agents - Understand what makes these applications successful as agents

Computer Use takes Claude's capabilities much further. It's a collection of tools that allow Claude to interact with a full desktop computer environment. 

You can also quickly add notes to your CLAUDE.md file using the # command. For example, typing # Always use descriptive variable names will prompt you to add this guideline to your project, local, or user memory.

# - Adds notes to your CLAUDE.md file

Step 1: Feed Context into Claude

Step 2: Tell Claude to Plan a Solution
Instead of jumping straight to implementation, ask Claude to think through the problem and create a plan. Tell Claude specifically not to write any code yet - just focus on the approach and steps needed.

Step 3: Ask Claude to Implement the Solution

### Test-Driven Development Workflow

- Feed context into Claude - Same as before, show Claude relevant files
- Ask Claude to think of test cases - Have Claude brainstorm what tests would validate your new feature
- Ask Claude to implement those tests - Select the most relevant tests and have Claude write them
- Ask Claude to write code that passes the tests - Claude will iterate on the implementation until all tests pass

### Setting Up an MCP Server

```
claude mcp add [server-name] [command-to-start-server]
claude mcp add documents uv run main.py
```

### Popular MCP Integrations

- sentry-mcp - Automatically discover and fix bugs logged in Sentry
- playwright-mcp - Gives Claude browser automation capabilities for testing and troubleshooting
- figma-context-mcp - Exposes Figma designs to Claude
- mcp-atlassian - Allows Claude to access Confluence and Jira
- firecrawl-mcp-server - Adds web scraping capabilities to Claude
- slack-mcp - Allows Claude to post messages or reply to specific threads

Git worktrees are the perfect tool for this workflow. If your project uses Git (which it should), you can use worktrees immediately. They're like an extension of Git's branching system that creates complete copies of your project in separate directories.

.trees 目录，然后需要 symlink .env 文件

### Computer use

Computer Use is a powerful feature that lets Claude interact directly with desktop environments, essentially giving the AI the ability to control a computer like a human would. This capability opens up entirely new possibilities for automation, testing, and workflow assistance.

This makes it incredibly useful for tasks like:

- Automated QA testing of web applications
- Data entry and form filling
- Website navigation and information gathering
- UI testing and validation
- Repetitive desktop tasks

  Computer Use 功能说明

  Computer Use 让我能够：
  1. 浏览器自动化: 导航网页、点击、输入、截图
  2. 桌面交互: 控制应用程序、文件操作
  3. 实时测试: 执行真实的用户交互场景
  4. 视觉验证: 捕获页面状态和元素位置

  这对 QA 测试、UI 验证和自动化工作流程非常有用。

TODO computer use https://github.com/anthropics/anthropic-quickstarts/tree/main/computer-use-demo

# - Adds notes to your CLAUDE.md file

Step 1: Feed Context into Claude

Step 2: Tell Claude to Plan a Solution
Instead of jumping straight to implementation, ask Claude to think through the problem and create a plan. Tell Claude specifically not to write any code yet - just focus on the approach and steps needed.

Step 3: Ask Claude to Implement the Solution

### Test-Driven Development Workflow

- Feed context into Claude - Same as before, show Claude relevant files
- Ask Claude to think of test cases - Have Claude brainstorm what tests would validate your new feature
- Ask Claude to implement those tests - Select the most relevant tests and have Claude write them
- Ask Claude to write code that passes the tests - Claude will iterate on the implementation until all tests pass

### Setting Up an MCP Server

```
claude mcp add [server-name] [command-to-start-server]
claude mcp add documents uv run main.py
```

### Popular MCP Integrations

- sentry-mcp - Automatically discover and fix bugs logged in Sentry
- playwright-mcp - Gives Claude browser automation capabilities for testing and troubleshooting
- figma-context-mcp - Exposes Figma designs to Claude
- mcp-atlassian - Allows Claude to access Confluence and Jira
- firecrawl-mcp-server - Adds web scraping capabilities to Claude
- slack-mcp - Allows Claude to post messages or reply to specific threads

Git worktrees are the perfect tool for this workflow. If your project uses Git (which it should), you can use worktrees immediately. They're like an extension of Git's branching system that creates complete copies of your project in separate directories.

.trees 目录，然后需要 symlink .env 文件

### Computer use

Computer Use is a powerful feature that lets Claude interact directly with desktop environments, essentially giving the AI the ability to control a computer like a human would. This capability opens up entirely new possibilities for automation, testing, and workflow assistance.

This makes it incredibly useful for tasks like:

- Automated QA testing of web applications
- Data entry and form filling
- Website navigation and information gathering
- UI testing and validation
- Repetitive desktop tasks

  Computer Use 功能说明

  Computer Use 让我能够：
  1. 浏览器自动化: 导航网页、点击、输入、截图
  2. 桌面交互: 控制应用程序、文件操作
  3. 实时测试: 执行真实的用户交互场景
  4. 视觉验证: 捕获页面状态和元素位置

  这对 QA 测试、UI 验证和自动化工作流程非常有用。

TODO computer use https://github.com/anthropics/anthropic-quickstarts/tree/main/computer-use-demo

# 2025-08-21

# MCP

## Python Syntax

Think of AsyncExitStack as a flexible “context-manager router”: you dynamically plug in any number of async context managers, and when the stack ends they are all awaited in clean, predictable order—no matter how complex the surrounding control flow became.

```
async with AsyncExitStack() as stack:
    # 1) Open an async context manager and register it
    conn = await stack.enter_async_context(get_connection())

    # 2) Maybe decide to open another later
    if need_temp_dir:
        tmpdir = await stack.enter_async_context(aiotemp.TemporaryDirectory())

    # ...do work...

special variables: __name__, __file__, __spec__, __builtins__, _. 解释器或调试器的内部状态；99 % 的业务调试场景用不到，所以被单独归为一组

function variables: 当前模块/函数里定义的 函数、lambda、类 对象，如 def helper(): ..., class MyDTO:

```
async with AsyncExitStack() as stack:
        doc_client = await stack.enter_async_context(
            MCPClient(command=command, args=args)
        )
        clients["doc_client"] = doc_client

        for i, server_script in enumerate(server_scripts):
            client_id = f"client_{i}_{server_script}"
            client = await stack.enter_async_context(
                MCPClient(command="uv", args=["run", server_script])
            )
            clients[client_id] = client

        chat = CliChat(
            doc_client=doc_client,
            clients=clients,
            claude_service=claude_service,
        )

        cli = CliApp(chat)
        await cli.initialize()
        await cli.run()
```

| 术语                                  | 作用                                                                                      | 关键点                                                                          |
| ----------------------------------- | --------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| **`AsyncExitStack`**                | 统一管理 *多个* **异步上下文管理器**                                                                  | 进入时逐个调用 `__aenter__` 并把返回值压栈；退出时按**后进先出**调用对应的 `__aexit__`，即使中途抛异常也能保证全部清理   |
| **`stack.enter_async_context(cm)`** | 把 `cm`（任意实现 `__aenter__/__aexit__` 的对象）注册进 `AsyncExitStack` 并立即 `await cm.__aenter__()` | 返回 `__aenter__` 的结果，供后续使用                                                    |
| **`MCPClient(...)`**                | 你项目里的异步客户端，用来启动子进程（`uv run ...`）并建立 IPC/WebSocket 之类的连接                                 | 本身是异步上下文管理器：<br> • `__aenter__` → 启动子进程 / 建链<br> • `__aexit__` → 关闭链路 / 终止进程 |
| **`CliChat / CliApp`**              | 命令行聊天 UI 的业务封装                                                                          | 初始化里接收各类 client，`run()` 开始事件循环                                               |

```
doc_client = await stack.enter_async_context(
    MCPClient(command=command, args=args)
)
clients["doc_client"] = doc_client
```

| 步骤                                   | 发生了什么？                                                                                                                         |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| `MCPClient(...)`                     | **实例化** 一个异步上下文管理器，准备去连「文档服务器」。这里还没真正连，只是造了对象。                                                                                 |
| `stack.enter_async_context(...)`     | ① 把这个对象注册到 `AsyncExitStack` —— **以后自动帮你释放资源**。<br>② 立即 `await obj.__aenter__()` → 真正**建立连接**。<br>③ 返回连接实例（比如一个 WebSocket 客户端）。 |
| `doc_client = ...`                   | 接收上一步返回的实例，变量名叫 `doc_client`。                                                                                                  |
| `clients["doc_client"] = doc_client` | 存进字典，方便后面统一访问：`clients["doc_client"]`。                                                                                         |

为什么要用 AsyncExitStack 而不是好多 async with？

```
async with MCPClient(...) as doc_client:
    async with MCPClient(...) as c0:
        async with MCPClient(...) as c1:
            ...

```

AsyncExitStack 让你：

平铺代码 —— 不论多少个 client 都一层缩进；

异常安全 —— 任意一步出错，栈会逆序调用所有 __aexit__ 关连接/杀进程；

可编程 —— 可以在循环里动态加上下文管理器。

```
    # 1）描述如何启动子进程的参数
    server_params = StdioServerParameters(
        command=self._command,        # "uv"
        args=self._args,              # ["run", "api.py"]
        env=self._env,                # 继承或自定义
    )

 # 2）启动子进程并拿到它的 stdin/stdout 管道
    stdio_transport = await self._exit_stack.enter_async_context(
        stdio_client(server_params)   # → (stdio_reader, stdio_writer)
    )
    _stdio, _write = stdio_transport

    # 3）基于管道创建「高级协议」的 ClientSession
    self._session = await self._exit_stack.enter_async_context(
        ClientSession(_stdio, _write) # LSP / JSON-RPC / 自定义协议
    )

    # 4）与服务器握手（如 LSP initialize）
    await self._session.initialize()
```

```
main.py
└── AsyncExitStack (主)
    ├─ MCPClient(doc_client)
    │   └── AsyncExitStack (子)
    │       ├─ stdio_client  → 文档服务器子进程
    │       └─ ClientSession → JSON-RPC/LSP 会话
    ├─ MCPClient(client_0_api.py)
    │   └── AsyncExitStack (子)
    │       ├─ stdio_client  → `uv run api.py`
    │       └── ClientSession
    └─ MCPClient(client_1_worker.py)
        └── AsyncExitStack (子)
            ├─ stdio_client  → `uv run worker.py`
            └── ClientSession
```



## workflow with mcp cli demo

1. Parse command-line input
2. Send to Claude and get response
3. If there are "tool_use" then `await ToolManager.execute_tool_requests()` get result
4. Send to Claude again until break

In `main.py` use the following code to init a MCP Client:

```
        doc_client = await stack.enter_async_context(
            MCPClient(command=command, args=args)
        )
        clients["doc_client"] = doc_client
```

MCPClient 将会 create Client session, ClientSession 是 mcp package 的，封装了一些 request function 用于跟 MCP Server 沟通和交流。

```
server_params = StdioServerParameters(
            command=self._command,
            args=self._args,
            env=self._env,
        )
        stdio_transport = await self._exit_stack.enter_async_context(
            stdio_client(server_params)
        )
        _stdio, _write = stdio_transport
        self._session = await self._exit_stack.enter_async_context(
            ClientSession(_stdio, _write)
        )
```

实际的通信使用 process 的 stdin/stdout 的 进程间通信 (IPC)。在本机 fork/exec 出服务器脚本，并通过标准输入输出做 IPC。

MCP Server 必须本地吗？如果是 Stdio transport 是需要本地的，还有 HTTP-SSE, WebSocket, gRPC 等方式，可以远程链接。在 mcp-server.py 文件里面有 `mcp.run(transport="stdio")` 这样的描述。

TODO 如何实现 MCP 的自主发现？目前是需要手动静态调用的，能不能动态调用？或者是动态的注册服务，需要传递某些参数进去，API 会返回不同的 MCP 和 Tools 等，然后再进行后续的流程。

MCP Server 在 `mcp_server.py` 文件里面，通过 @mcp.tool @mcp.resource 等 decorators 来注册相关方法到 mcp instance 上面，最后通过 mcp.run 启动 server，然后由 MCPClient 调用。

MCPClient 通过 session function 发送请求获取相关信息：

```
async def list_tools(self) -> list[types.Tool]:
        result = await self.session().list_tools()
        return result.tools

    async def call_tool(
        self, tool_name: str, tool_input
    ) -> types.CallToolResult | None:
        return await self.session().call_tool(tool_name, tool_input)
```

说实话，这个流程是真的绕，没有什么更好的办法了吗？

## MCP Resource

Resources in MCP servers allow you to expose data to clients, similar to GET request handlers in a typical HTTP server. They're perfect for scenarios where you need to fetch information rather than perform actions.

Resources follow a request-response pattern. Your client sends a ReadResourceRequest with a URI, and the MCP server responds with the data. The URI acts like an address for the resource you want to access.

```
@mcp.resource("docs://documents/{doc_id}", mime_type="text/plain")
def fetch_doc(doc_id: str) -> str:
    if doc_id not in docs:
        raise ValueError(f"Doc with id {doc_id} not found")
    return docs[doc_id]
```

Support template resources.

When you've defined resources on your MCP server, your client needs a way to request and use them. The client acts as a bridge between your application and the MCP server, handling the communication and data parsing automatically.

The flow is straightforward: when a user wants to reference a document (like typing "@report.pdf"), your application uses the MCP client to fetch that resource from the server and include its contents directly in the prompt sent to Claude.

## Prompts

Prompts in MCP servers let you define pre-built, high-quality instructions that clients can use instead of writing their own prompts from scratch. Think of them as carefully crafted templates that give better results than what users might come up with on their own.

The basic structure looks like this:

- Define prompts using the @mcp.prompt() decorator
- Add a name and description for each prompt
- Return a list of messages that form the complete prompt
- These prompts should be high quality, well-tested, and relevant to your MCP server's purpose

```
from mcp.server.fastmcp import base

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

Remember that prompts are meant to provide value that users couldn't easily get on their own - they should represent your expertise in the domain your MCP server covers.

The first step is implementing the list_prompts method in your MCP client. This method retrieves all available prompts from the server:

```
async def list_prompts(self) -> list[types.Prompt]:
    result = await self.session().list_prompts()
    return result.prompts

async def get_prompt(self, prompt_name, args: dict[str, str]):
    result = await self.session().get_prompt(prompt_name, args)
    return result.messages
```

# <-- all acquired resources are released here automatically
```

enter_async_context(cm) — add one async context manager to the stack

result = await stack.enter_async_context(cm)

Validate protocol
Internally it checks that cm implements both __aenter__ and __aexit__; otherwise it raises TypeError.

Run __aenter__

result = await cm.__aenter__()


Whatever that coroutine returns is forwarded back to you (often the resource handle).

Register cleanup
It binds cm.__aexit__ to cm and pushes the resulting coroutine-function onto the stack.
Later, when the AsyncExitStack itself leaves its async with block, it will await that stored __aexit__, handing it any exception details just like the interpreter does for a regular async with.

```
super().__init__()
```

# 2025-08-20

# MCP

The MCP client provides these capabilities through simple method calls that our application code can use.

To test our implementation, we can run the client directly. The file includes a testing harness that connects to our MCP server and calls our methods:

```
async with MCPClient(
    command="uv", args=["run", "mcp_server.py"]
) as client:
    result = await client.list_tools()
    print(result)
```

When we run our main application and ask Claude about a document:

- Our code uses the client to get available tools
- These tools are sent to Claude along with the user's question
- Claude decides to use the read_doc_contents tool
- Our code uses the client to execute that tool
- The result is sent back to Claude, who then responds to the user

For example, asking "What is the contents of the report.pdf document?" will trigger Claude to use our document reading tool, and we'll get back information about the 20m condenser tower document we set up in our server.

## Some Python syntax notes

Started reading and learning Python through actual code, some notes here:

```
@mcp.tool(
    name="read_doc_contents",
    description="Read the contents of a document and return it as a string.",
)
def read_document(
    doc_id: str = Field(description="Id of the document to read"),
):
    ...
```

What the decorator does

- @mcp.tool(...) is a decorator.
- When the interpreter executes this file, it calls mcp.tool(…), which returns another function (the wrapper).
- That wrapper immediately receives read_document and registers it inside the mcp instance with the supplied name and description.
- The result is that the raw Python function becomes discoverable and callable by the MCP runtime.

Inside of mcp package, the implementation is:

```
def tool(
        self,
        name_or_fn: str | AnyFunction | None = None,
        *,
        name: str | None = None,
        title: str | None = None,
        description: str | None = None,
        tags: set[str] | None = None,
        output_schema: dict[str, Any] | None | NotSetT = NotSet,
        annotations: ToolAnnotations | dict[str, Any] | None = None,
        exclude_args: list[str] | None = None,
        meta: dict[str, Any] | None = None,
        enabled: bool | None = None,
    ) -> Callable[[AnyFunction], FunctionTool] | FunctionTool:
        """Decorator to register a tool.

        Tools can optionally request a Context object by adding a parameter with the
        Context type annotation. The context provides access to MCP capabilities like
        logging, progress reporting, and resource access.

        This decorator supports multiple calling patterns:
        - @server.tool (without parentheses)
        - @server.tool (with empty parentheses)
        - @server.tool("custom_name") (with name as first argument)
        - @server.tool(name="custom_name") (with name as keyword argument)
        - server.tool(function, name="custom_name") (direct function call)

        Args:
            name_or_fn: Either a function (when used as @tool), a string name, or None
            name: Optional name for the tool (keyword-only, alternative to name_or_fn)
            description: Optional description of what the tool does
            tags: Optional set of tags for categorizing the tool
            output_schema: Optional JSON schema for the tool's output
            annotations: Optional annotations about the tool's behavior
            exclude_args: Optional list of argument names to exclude from the tool schema
            meta: Optional meta information about the tool
            enabled: Optional boolean to enable or disable the tool

        Examples:
            Register a tool with a custom name:
            ```python
            @server.tool
            def my_tool(x: int) -> str:
                return str(x)

            # Register a tool with a custom name
            @server.tool
            def my_tool(x: int) -> str:
                return str(x)

            @server.tool("custom_name")
            def my_tool(x: int) -> str:
                return str(x)

            @server.tool(name="custom_name")
            def my_tool(x: int) -> str:
                return str(x)

            # Direct function call
            server.tool(my_function, name="custom_name")
            ```
        """
        if isinstance(annotations, dict):
            annotations = ToolAnnotations(**annotations)

        if isinstance(name_or_fn, classmethod):
            raise ValueError(
                inspect.cleandoc(
                    """
                    To decorate a classmethod, first define the method and then call
                    tool() directly on the method instead of using it as a
                    decorator. See https://gofastmcp.com/patterns/decorating-methods
                    for examples and more information.
                    """
                )
            )

        # Determine the actual name and function based on the calling pattern
        if inspect.isroutine(name_or_fn):
            # Case 1: @tool (without parens) - function passed directly
            # Case 2: direct call like tool(fn, name="something")
            fn = name_or_fn
            tool_name = name  # Use keyword name if provided, otherwise None

            # Register the tool immediately and return the tool object
            tool = Tool.from_function(
                fn,
                name=tool_name,
                title=title,
                description=description,
                tags=tags,
                output_schema=output_schema,
                annotations=annotations,
                exclude_args=exclude_args,
                meta=meta,
                serializer=self._tool_serializer,
                enabled=enabled,
            )
            self.add_tool(tool)
            return tool

        elif isinstance(name_or_fn, str):
            # Case 3: @tool("custom_name") - name passed as first argument
            if name is not None:
                raise TypeError(
                    "Cannot specify both a name as first argument and as keyword argument. "
                    f"Use either @tool('{name_or_fn}') or @tool(name='{name}'), not both."
                )
            tool_name = name_or_fn
        elif name_or_fn is None:
            # Case 4: @tool or @tool(name="something") - use keyword name
            tool_name = name
        else:
            raise TypeError(
                f"First argument to @tool must be a function, string, or None, got {type(name_or_fn)}"
            )

        # Return partial for cases where we need to wait for the function
        return partial(
            self.tool,
            name=tool_name,
            title=title,
            description=description,
            tags=tags,
            output_schema=output_schema,
            annotations=annotations,
            exclude_args=exclude_args,
            meta=meta,
            enabled=enabled,
        )

```
The important line of code is `self.add_tool(tool)` and the source code is:
```
    def add_tool(self, tool: Tool) -> Tool:
        """Add a tool to the server.

        The tool function can optionally request a Context object by adding a parameter
        with the Context type annotation. See the @tool decorator for examples.

        Args:
            tool: The Tool instance to register

        Returns:
            The tool instance that was added to the server.
        """
        self._tool_manager.add_tool(tool)

        # Send notification if we're in a request context
        try:
            from fastmcp.server.dependencies import get_context

            context = get_context()
            context._queue_tool_list_changed()  # type: ignore[private-use]
        except RuntimeError:
            pass  # No context available

        return tool
```

So `@mcp.tool` decorator is used for register the function to the MCP instance so that it can be found later.

```
class AsyncExitStack(_BaseExitStack, AbstractAsyncContextManager):
  """Async context manager for dynamic management of a stack of exit callbacks."""
```

Multiple inheritance. AsyncExitStack gains methods & attributes from both parents. Triple-quoted string placed first in the class body ⇒ becomes AsyncExitStack.__doc__.

```
@staticmethod
def _create_async_exit_wrapper(cm, cm_exit):
    return MethodType(cm_exit, cm)
```

@staticmethod tells Python not to pass self or cls; the function is stored on the class unmodified.

Double-underscore names (__aenter__, __aexit__) are magic / dunder methods defined by language specs.

Single-leading-underscore (_push_async_cm_exit) is simply “internal use” per PEP 8.

self: Pure convention. Python injects the first positional argument; the community settled on self so you always know what it is. Instance methods (defined with def method(self, …)). The instance on which the method was called.

cls: Same convention; lets the body call cls() to construct objects or look up class attributes. The class object itself ( - not an instance) Class methods (decorated with @classmethod, i.e. def make(cls, …))

```
cls = type(cm)
try:
    _enter = cls.__aenter__
    _exit  = cls.__aexit__
except AttributeError:
    raise TypeError(
        f"'{cls.__module__}.{cls.__qualname__}' object does "
        f"not support the asynchronous context manager protocol"
    ) from None
```

Try catch.

Awaiting and pushing the exit handler

```
result = await _enter(cm)        # Calls cm.__aenter__()
self._push_async_cm_exit(cm, _exit)
return result
```

- await suspends until cm.__aenter__ resolves.
- The helper _push_async_cm_exit (inherited) records the bound __aexit__ so that the stack can later await it in reverse order.
- The value produced by __aenter__ is bubbled back to the caller.

TODO figure out the workflow of async await and __aenter__.

__aenter__ & __aexit__ (async context manager protocol)

Python has two context-manager protocols:

| Purpose              | Sync                                    | Async                                          |
| -------------------- | --------------------------------------- | ---------------------------------------------- |
| Enter resource scope | `obj.__enter__(self)`                   | `await obj.__aenter__(self)`                   |
| Exit / cleanup       | `obj.__exit__(self, exc_type, exc, tb)` | `await obj.__aexit__(self, exc_type, exc, tb)` |

The “script guard”

```
if __name__ == "__main__":
```

Runs the enclosed code only when the file is executed directly (python myfile.py), not when it is imported as a module.

asyncio.run(main())

High-level helper (Python 3.7+) that:

- Creates a fresh event loop.
- Runs main() until its coroutine completes.
- Shuts the loop down cleanly and propagates any uncaught exceptions.

# 2025-08-19

# MCP

Building an MCP server becomes much simpler when you use the official Python SDK. Instead of manually writing complex JSON schemas for tools, the SDK handles all that complexity for you with decorators and type hints.

```
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("DocumentMCP", log_level="ERROR")
```

The MCP Python SDK transforms what used to be a complex process of writing tool definitions into something that feels natural for Python developers. You focus on the business logic while the SDK handles the protocol details.

![](https://everpath-course-content.s3-accelerate.amazonaws.com/instructor%2Fa46l9irobhg0f5webscixp0bs%2Fpublic%2F1748542727%2F09_-_005_-_The_Server_Inspector_07.1748542727369.jpg)

# 2025-08-18

# MCP

Model Context Protocol (MCP) is a communication layer that provides Claude with context and tools without requiring you to write a bunch of tedious integration code.

an MCP Client (your server) connecting to MCP Servers that contain tools, prompts, and resources. Each MCP server acts as an interface to some outside service.

MCP Client -> MCP Servers (tools, prompts, and resources) -> Outside Service

Old tool functions way for "What open pull requests are there across all my repositories?":

- create incredible number of tool schemas and functions and put them in the prompt

MCP way:

- shifts the tool definitions and execution to MCP servers
- MCP server encapsulates GitHub's functionality, you can use it directly without having to implement them
- MCP servers provide access to data or functionality implemented by outside services. They package up complex integrations into reusable components that any application can connect to.

service providers themselves will make their own official MCP implementations.

MSO = MCP Search Optimization will be emerged soon

MCP servers and tool use are complementary but different concepts.

The MCP client serves as the communication bridge between your server and MCP servers. Think of it as your access point to all the tools that an MCP server provides. When you need to use external tools or services, the client handles all the message passing and protocol details for you.

One of MCP's key strengths is being transport agnostic - a fancy way of saying the client and server can talk to each other using different communication methods.

MCP clients and servers can connect over:

- Standard IO
- HTTP
- WebSockets
- Various other network protocols

Once connected, the client and server exchange specific message types defined in the MCP specification. The main message types you'll work with are:

- ListToolsRequest/ListToolsResult: The client asks the server "what tools do you provide?" and gets back a list of available tools.
- CallToolRequest/CallToolResult: The client asks the server to run a specific tool with certain arguments, then receives the results.

Workflow:

![](https://everpath-course-content.s3-accelerate.amazonaws.com/instructor%2Fa46l9irobhg0f5webscixp0bs%2Fpublic%2F1748542651%2F09_-_002_-_MCP_Clients_18.1748542650970.jpg)

Roles:

- Our Server: your server backend or interface

Processes:

1. User sends query
2. Our Server get tools from MCP Server by MCP Client
3. Send all to Claude -> ToolUse
4. Our Server invokes functions on MCP Server by MCP Client and get responses
5. send all to Claude -> final response to User

TODO how does MCP registration work? how MCP client determine which MCP server should the Server use?

# 2025-08-17

# Features of Claude

## Prompt Chaching

Prompt Chaching is particularly valuable when you're sending:

- Large system prompts (like a 6K token coding assistant prompt)
- Complex tool schemas (around 1.7K tokens for multiple tools)
- Repeated message content

The key insight is that caching only helps if you're repeatedly sending identical content - but in many applications, this happens extremely frequently.

```
last_tool["cache_control"] = {"type": "ephemeral"}
```

Add Schema Caching to the tool.

```
if system:
    params["system"] = [
        {
            "type": "text",
            "text": system,
            "cache_control": {"type": "ephemeral"}
        }
    ]
```

Prompt caching is most effective when you have:

- Consistent tool schemas across requests
- Stable system prompts
- Applications that make multiple requests with similar context

Remember that the cache only lasts for one hour, so it's designed for applications with relatively frequent API usage rather than long-term storage.

## Files API

The Files API provides an alternative way to handle file uploads. Instead of encoding images or PDFs directly in your messages as base64 data, you can upload files ahead of time and reference them later.

Here's how it works:

- Upload your file (image, PDF, text, etc.) to Claude using a separate API call
- Receive a file metadata object containing a unique file ID
- Reference that file ID in future messages instead of including raw file data

## Code Execution Tool

Code execution is a server-based tool that doesn't require you to provide an implementation. You simply include a predefined tool schema in your request, and Claude can optionally execute Python code in an isolated Docker container.

Key characteristics of the code execution environment:

- Runs in an isolated Docker container
- No network access (can't make external API calls)
- Claude can execute code multiple times during a single conversation
- Results are captured and interpreted by Claude for the final response

## Combining Files API and Code Execution

Here's a typical workflow:

- Upload your data file (like a CSV) using the Files API
- Include a container upload block in your message with the file ID
- Ask Claude to analyze the data
- Claude writes and executes code to process your file
- Claude can generate outputs (like plots) that you can download

```
messages = []
add_user_message(
    messages,
    [
        {
            "type": "text",
            "text": """Run a detailed analysis to determine major drivers of churn.
            Your final output should include at least one detailed plot summarizing your findings."""
        },
        {"type": "container_upload", "file_id": file_metadata.id},
    ],
)

chat(
    messages,
    tools=[{"type": "code_execution_20250522", "name": "code_execution"}]
)
```

many possibilities:

- Image processing and manipulation
- Document parsing and transformation
- Mathematical computations and modeling
- Report generation with custom formatting

# 2025-08-15

# Features of Claude

## PDF 

Claude can read and analyze PDF files directly, making it a powerful tool for document processing. This capability works similarly to image processing, but with a few key differences in how you structure your code.

### Key Changes from Image Processing

When adapting your image processing code for PDFs, you need to update several elements:

- Change the file extension from .png to .pdf
- Update the variable name from image_bytes to file_bytes for clarity
- Set the type to "document" instead of "image"
- Change the media type to "application/pdf" instead of "image/png"

This makes Claude essentially a one-stop solution for extracting any type of information from PDF documents, whether you need summaries, data analysis, or specific content extraction.

## Citations

Imagine asking Claude about how Earth's atmosphere formed and getting a detailed answer. Without citations, users have no way to verify the information or understand that Claude is actually referencing a specific document you provided. Citations solve this transparency problem by creating a clear trail from Claude's response back to your source material.

### Enabling Citations

To enable citations, you need to modify your document message structure. Add two new fields to your document block:

```
{
    "type": "document",
    "source": {
        "type": "base64",
        "media_type": "application/pdf",
        "data": file_bytes,
    },
    "title": "earth.pdf",
    "citations": { "enabled": True }
}
```

The title field gives your document a readable name, while citations: {"enabled": True} tells Claude to track where it finds information.

## Prompt caching

Prompt caching is a feature that speeds up Claude's responses and reduces the cost of text generation by reusing computational work from previous requests. Instead of throwing away all the processing work after each request, Claude can save and reuse it when you send similar content again.

After sending you the response, Claude throws away all this computational work - the tokenization, embeddings, and context analysis all get discarded.

This becomes inefficient when you make follow-up requests that include the same content.

However, there are important limitations to keep in mind:

- Cache duration: Cached content only lives for one hour
- Limited use cases: Only beneficial when you're repeatedly sending the same content
- High frequency requirement: Most effective when the same content appears extremely frequently in your requests

The process is straightforward: your initial request writes processing work to the cache, and follow-up requests can read from that cache instead of reprocessing the same content. The cache lives for one hour, so this feature is only useful if you're repeatedly sending the same content within that timeframe.

Caching isn't enabled automatically - you need to manually add cache breakpoints to specific blocks in your messages. When you place a cache breakpoint in a message, Claude caches all the processing work up to and including that breakpoint. Content after the breakpoint is processed normally without caching.

so you must use the expanded format with the cache_control field set to {"type": "ephemeral"}. For the cache to be useful in follow-up requests, the content must be identical up to the breakpoint.

You're not limited to text blocks - cache breakpoints can be added to:

- System prompts
- Tool definitions
- Image blocks
- Tool use and tool result blocks

Seems very useful for multi-conversion usage, used for providing context.

Behind the scenes, Claude processes your request components in a specific order: tools first, then system prompt, then messages. Understanding this order helps you place breakpoints effectively.

You can add up to four cache breakpoints total.

There's a minimum threshold for caching: content must be at least 1024 tokens long to be cached. This is the sum of all messages and blocks you're trying to cache, not individual blocks.

The key to effective prompt caching is identifying which parts of your requests stay consistent across multiple calls and placing breakpoints strategically to maximize reuse while minimizing cache invalidation.

# 2025-08-14

# Features of Claude

## Image

Claude's vision capabilities let you include images in your messages and ask Claude to analyze them in countless ways. You can ask Claude to describe what's in an image, compare multiple images, count objects, or perform complex visual analysis tasks.

There are several important limitations to keep in mind when working with images:

- Up to 100 images across all messages in a single request
- Max size of 5MB per image
- When sending one image: max height/width of 8000px
- When sending multiple images: max height/width of 2000px
- Images can be included as base64 encoding or a URL to the image
- Each image counts as tokens based on its dimensions: tokens = (width px × height px) / 750

The key to getting good results with images is applying the same prompting engineering techniques you'd use with text. Simple prompts often lead to poor results. For example, asking "How many marbles are in this image?" might return an incorrect count.

# 2025-08-13

# Features of Claude

## Extended Thinking

When extended thinking is enabled, Claude's response changes from a simple text block to a structured response containing two parts:

- Thinking Block
- Text Block

With thinking enabled, you get both the reasoning process and the final answer:

- Signature: A cryptographic token that ensures you haven't modified the thinking text.

When to Use Extended Thinking
The decision is straightforward: use your prompt evaluations. Run your prompts without thinking first, and if the accuracy isn't meeting your requirements after you've already optimized your prompt, then consider enabling extended thinking.

# 2025-08-12

# RAG

How Re-ranking Works

Re-ranking is conceptually simple. After running your vector index and BM25 index and merging the results, you add one more step: a re-ranker that uses Claude to intelligently reorder your search results.

The Re-ranking Prompt

```
You are tasked with finding the documents most relevant to a user's question.

<user_question>
What happened with INC-2023-Q4-011?
</user_question>

Here are documents that may be relevant:
<documents>
<document>Section 10...</document>
<document>Section 2...</document>
<document>Section 7...</document>
<document>Section 6...</document>
</documents>

Return the 3 most relevant docs, in order of decreasing relevance.
```

Efficiency Considerations

A better approach is to assign each text chunk a unique ID ahead of time, then ask Claude to return just those IDs in the correct order:

```
<documents>
<document>
<id>ab84</id>
<content>Section 10...</content>
</document>
<document>
<id>51n3</id>
<content>Section 8...</content>
</document>
</documents>
```
Claude can then return a simple list like ["1p5g", "51n3", "ab83"] instead of copying entire text chunks.

Werid, my output order is incorrect.

## Contextual retrieval

Contextual retrieval is a technique that improves RAG pipeline accuracy by solving a fundamental problem: when you split a document into chunks, each chunk loses its connection to the broader document context.

Contextual retrieval adds a preprocessing step before inserting chunks into your retriever database. Here's the process:

- Take each individual chunk and the original source document
- Send both to Claude with a specific prompt
- Ask Claude to write a short snippet that situates the chunk within the overall document
- Combine this context with the original chunk to create a "contextualized chunk"
- Use the contextualized chunk in your vector and BM25 indexes

Handling Large Documents

you can provide a reduced set of context instead of the entire document. The strategy is to include:

- A few chunks from the start of the document (often containing summaries or abstracts)
- Chunks immediately before the chunk you're contextualizing
- Skip chunks in the middle that are less relevant to the current chunk

This approach gives Claude enough context to understand what the document is about and how the current chunk fits in, without overwhelming the prompt with unnecessary text.

# 2025-08-11

# RAG

## Step 1: Chunking the Text

```
with open("./report.md", "r") as f:
    text = f.read()

chunks = chunk_by_section(text)
chunks[2]  # Test to see the table of contents
```

## Step 2: Generate Embeddings

```
embeddings = generate_embedding(chunks)
```

## Step 3: Store in Vector Database

```
store = VectorIndex()

for embedding, chunk in zip(embeddings, chunks):
    store.add_vector(embedding, {"content": chunk})
```

Notice that we store both the embedding and the original text content. This is crucial because when we search later, we need to return the actual text, not just the numerical embedding values.

The embedding numbers are just indexes for the chunk.

## Step 4: Process User Queries

```
user_embedding = generate_embedding("What did the software engineering dept do last year?")
```

## Step 5: Find Relevant Content

```
results = store.search(user_embedding, 2)

for doc, distance in results:
    print(distance, "\n", doc["content"][0:200], "\n")
```

## bm25

When building RAG pipelines, you'll quickly discover that semantic search alone doesn't always return the best results. Sometimes you need exact term matches that semantic search might miss. The solution is to combine semantic search with lexical search using a technique called BM25.

While semantic search excels at understanding context and meaning, it might return sections that are semantically related but don't actually contain the exact term you're looking for.

The solution is to run both semantic and lexical searches in parallel, then merge the results.

- Semantic search finds conceptually related content using embeddings
- Lexical search finds exact term matches using classic text search
- Merged results combine both approaches for better accuracy

BM25 (Best Match 25) is a popular algorithm for lexical search in RAG systems. Here's how it processes a search query:

Step 1: Tokenize the query
Break the user's question into individual terms. For example, "a INC-2023-Q4-011" becomes ["a", "INC-2023-Q4-011"].

Step 2: Count term frequency
See how often each term appears across all your documents. Common words like "a" might appear 5 times, while specific terms like "INC-2023-Q4-011" might appear only once.

Step 3: Weight terms by importance
Terms that appear less frequently get higher importance scores. The word "a" gets low importance because it's common, while "INC-2023-Q4-011" gets high importance because it's rare.

Step 4: Find best matches
Return documents that contain more instances of the higher-weighted terms.

The BM25 algorithm prioritizes sections that actually contain your specific search terms, especially rare terms like incident IDs.

BM25 excels at finding exact matches because it:

- Gives higher weight to rare, specific terms
- Ignores common words that don't add search value
- Focuses on term frequency rather than semantic meaning
- Works especially well for technical terms, IDs, and specific phrases

## Retriever

Merging results from different search methods isn't as simple as just concatenating lists. Each method uses different scoring systems, so we need a way to normalize and combine their rankings fairly.

Let's say we search for information about "INC-2023-Q4-011" and get these results:

- VectorIndex returns: Section 2 (rank 1), Section 7 (rank 2), Section 6 (rank 3)
- BM25Index returns: Section 6 (rank 1), Section 2 (rank 2), Section 7 (rank 3)

We combine these into a single table showing each text chunk's rank from both indexes, then apply the RRF formula:

```
RRF_score(d) = Σ(1 / (k + rank_i(d)))
```

For our example:

- Section 2: 1.0/(1+1) + 1.0/(1+2) = 0.833 Final rank: 1
- Section 7: 1.0/(1+2) + 1.0/(1+3) = 0.583 2
- Section 6: 1.0/(1+3) + 1.0/(1+1) = 0.75 3

# 2025-08-09

# RAG

After breaking a document into chunks, the next step in a RAG pipeline is finding which chunks are most relevant to a user's question. This is essentially a search problem - you need to look through all your text chunks and identify the ones that relate to what the user is asking about.

## Semantic Search

The most common approach for finding relevant chunks is semantic search. semantic search uses text embeddings to understand the meaning and context of both the user's question and each text chunk. 

## Text Embeddings

A text embedding is a numerical representation of the meaning contained in some text. 

- You feed text into an embedding model
- The model outputs a long list of numbers (the embedding)
- Each number ranges from -1 to +1
- These numbers represent different qualities or features of the input text

Each number in an embedding is essentially a "score" for some quality of the input text. However, here's the important caveat: we don't know precisely what each number represents.

So it looks like magic, and it is generated by embedding model. While it's helpful to imagine that one number might represent "how happy the text is" or "how much the text talks about oceans," these are just conceptual examples.

## RAG pipeline step by step

### Step 1: Chunk Your Source Text

break it into manageable chunks

### Step 2: Generate Embeddings

we convert each text chunk into numerical embeddings using an embedding model.

The embedding API typically performs a normalization step that scales each vector to have a magnitude of 1.0. This gives us normalized vectors like [0.944, 0.331] and [0.295, 0.955].

### Step 3: Store in Vector Database

We store these embeddings in a vector database - a specialized database optimized for storing, comparing, and searching through long lists of numbers like our embeddings.

Above are preprocessing works.

### Step 4: Process User Query

When a user asks a question like "I'm curious about the company. In particular, what did the software engineering dept do this year?", we run their query through the same embedding model.

So with the same embedding model, we can get similarity to match (I guess?).

### Step 5: Find Similar Embeddings

We send the user's query embedding to our vector database and ask it to find the most similar stored embeddings.

The vector database calculates Cosine Similarity to determine related items.

![](https://everpath-course-content.s3-accelerate.amazonaws.com/instructor%2Fa46l9irobhg0f5webscixp0bs%2Fpublic%2F1748542288%2F07_-_004_-_The_Full_RAG_Flow_15.1748542288516.jpg)

Key points about cosine similarity:

- Results range from -1 to 1
- Values close to 1 mean high similarity
- Values close to -1 mean very different
- 0 means perpendicular (no relationship)

Cosine Distance is simply calculated as (1 - cosine similarity): Values close to 0 mean high similarity and larger values mean less similarity.

### Step 6: Create the Final Prompt

Finally, we take the user's question and the most relevant text chunk we found, combine them into a prompt, and send it to Claude for a response.

# 2025-08-08

I asked a question regarding "word_count": How does word_count work? Claude: The key insight is that LLMs don't truly "predict" future token generation in a deterministic way - they generate probabilistically token by token. Any early word_count is either based on already-generated content or is an estimate that could be wrong.

# RAG

Retrieval Augmented Generation (RAG) is a technique that helps you work with large documents that are too big to fit into a single prompt. Instead of cramming everything into one massive prompt, RAG breaks documents into chunks and only includes the most relevant pieces when answering questions.

RAG takes a smarter approach. First, you break the document into smaller chunks during a preprocessing step. Then, when a user asks a question, you find the chunks most relevant to their question and only include those in your prompt.

Challenges with RAG

- Requires a preprocessing step to chunk documents
- Need a search mechanism to find "relevant" chunks
- Included chunks might not contain all the context Claude needs
- Many ways to chunk text - which approach is best?

For example, you could split documents into equal-sized portions, or you could create chunks based on document structure like headers and sections. Each approach has trade-offs you'll need to evaluate for your specific use case.

Text chunking is one of the most critical steps in building a RAG (Retrieval Augmented Generation) pipeline. How you break up your documents directly impacts the quality of your entire system. A poor chunking strategy can lead to irrelevant context being inserted into your prompts, causing your AI to give completely wrong answers.

### Size-Based Chunking

downsides:

- Words get cut off mid-sentence
- Chunks lose important context from surrounding text
- Section headers might be separated from their content

To address these issues, you can add overlap between chunks.

### Structure-Based Chunking

Structure-based chunking divides text based on the document's natural structure - headers, paragraphs, and sections. This works great when you have well-formatted documents like Markdown files.

### Semantic-Based Chunking

Semantic-based chunking is the most sophisticated approach. You divide text into sentences, then use natural language processing to determine how related consecutive sentences are. You build chunks from groups of related sentences.

### Sentence-Based Chunking

A practical middle ground is chunking by sentences. You split the text into individual sentences using regular expressions, then group them into chunks with optional overlap:

Remember: there's no single "best" chunking strategy. The right approach depends on your specific documents, use cases, and the trade-offs you're willing to make between implementation complexity and chunk quality.

# 2025-08-07

# Tool Use

With streaming enabled, for tool use, you'll also need to handle a new event type called InputJsonEvent.

Each InputJsonEvent contains two key properties:

- partial_json - A chunk of JSON representing part of the tool arguments
- snapshot - The cumulative JSON built up from all chunks received so far

Here's how you handle these events in your streaming pipeline:

```
for chunk in stream:
    if chunk.type == "input_json":
        # Process the partial JSON chunk
        print(chunk.partial_json)
        # Or use the complete snapshot so far
        current_args = chunk.snapshot
```

How JSON Validation Works

The Anthropic API doesn't immediately send you every chunk as Claude generates it. Instead, it buffers chunks and validates them first. This is enabled by default. The API will:

- Wait until the entire abstract value is complete
- Validate that key-value pair against your schema
- Send all the buffered chunks for abstract at once
- Repeat the process for the meta object

Fine-Grained Tool Calling

If you need faster, more granular streaming - perhaps to show users immediate updates or start processing partial results quickly - you can enable fine-grained tool calling.

Fine-grained tool calling does one main thing: it disables JSON validation on the API side. This means:

You get chunks as soon as Claude generates them
No buffering delays between top-level keys
More traditional streaming behavior
Critical: JSON validation is disabled - your code must handle invalid JSON
Enable it by adding fine_grained=True to your API call:

```
run_conversation(
    messages, 
    tools=[save_article_schema], 
    fine_grained=True
)
```

With fine-grained tool calling, you might receive a word_count value much earlier in the stream, without waiting for the entire meta object to be completed.

TODO interesting, how does word_count work? How AI predict how mange words while generating?

Without fine-grained tool calling, the API's validation would catch this error and potentially wrap problematic values in strings, which might not match your expected schema.

Consider enabling fine-grained tool calling when:

- You need to show users real-time progress on tool argument generation
- You want to start processing partial tool results as quickly as possible
- The buffering delays negatively impact your user experience
- You're comfortable implementing robust JSON error handling

For most applications, the default behavior with validation is perfectly adequate. But when you need that extra responsiveness, fine-grained tool calling gives you the control to get chunks as fast as Claude can generate them.

## The text edit tool

Important Note: Tool version strings can for all model versions can be found here: https://docs.anthropic.com/en/docs/agents-and-tools/tool-use/text-editor-tool

Claude comes with one built-in tool that you don't need to create from scratch: the text editor tool. This tool gives Claude the ability to work with files and directories just like you would in a standard text editor.

Here's where things get a bit confusing: while the tool schema is built into Claude, you still need to provide the actual implementation. This is the right design, as we can custom the access control and beheavier of the file handling implementation.

## Web search tool

Important note: Your organization must enable the Web Search tool in the settings console before using it. You can find this setting here: https://console.anthropic.com/settings/privacy

Claude includes a built-in web search tool that lets it search the internet for current or specialized information to answer user questions. Unlike other tools where you need to provide the implementation, Claude handles the entire search process automatically - you just need to provide a simple schema to enable it.

To use the web search tool, you create a schema object with these required fields:

web_search_schema = {
    "type": "web_search_20250305",
    "name": "web_search", 
    "max_uses": 5
}

The max_uses field limits how many searches Claude can perform. Claude might do follow-up searches based on initial results, so this prevents excessive API calls. A single search returns multiple results, but Claude may decide additional searches are needed.

You can get the WebSearchToolResultBlock, etc. from the response. You can also restrict search domains etc.

# 2025-08-05

# Tool Use

When you need structured data from Claude, you have two main approaches: prompt-based techniques using message prefills and stop sequences, or a more robust method using tools. 

While the prompt-based approach is simpler to set up, tools provide more reliable output at the cost of additional complexity.

Instead of hoping Claude formats its response correctly, you're essentially giving Claude a function to call with specific parameters that match your desired output structure.

Here's how the process works:

- Write a schema that describes the structure of data you're looking for
- Force Claude to use a tool with the tool_choice parameter
- Extract the structured data from the tool use response
- No need to provide a follow-up response - you're done once you get the data

tool_choice

- {"type": "auto"} - Model decides if it needs to use a tool (default)
- {"type": "any"} - Model must use a tool, but can choose which one
- {"type": "tool", "name": "TOOL_NAME"} - Model must use the specified tool

# 2025-08-04

## Tool  Use

The Multi-Turn Tool Pattern

Here's what happens behind the scenes when Claude needs multiple tools:

- User asks: "What day is 103 days from today?"
- Claude responds with a tool use block requesting get_current_datetime
- Your server calls the function and returns the result
- Claude realizes it needs more information and requests add_duration_to_datetime
- Your server calls that function and returns the result
- Claude now has enough information to provide the final answer

Building a Conversation Loop

```
def run_conversation(messages):
    while True:
        response = chat(messages)
        
        add_user_message(messages, response)
        
        if response.stop_reason != "tool_use":
            break
            
        tool_result_blocks = run_tools(response)
        add_user_message(tool_result_blocks)
        
    return messages

```

Bascially, just a deadloop to query Claude multiple times (with full context) until they think no more tool needed and combine all information and give it back to you. I think there might be some cache logic in Claude for saving some resources?

TODO Since there are so many tools, we may need to train a lightweight routing model to find and compose the relevant ones, then pass the consolidated results to Claude.

### The batch tool

The above way creates unnecessary back-and-forth communication when the operations could be performed simultaneously.

A batch tool is an additional tool that accepts a list of calls to other tools. Instead of Claude calling multiple tools directly, it calls the batch tool and provides arguments describing which tools it wants to run.

Rather than batch run tools on the server, you will define a batch_tool schema and send it to Claude to give you a "batch tool" tool call, and then you run the tool logic. In this way, you don't need to concern about the dependencies between tool calls.

```
batch_tool_schema = {
    "name": "batch_tool",
    "description": "Invoke multiple other tool calls simultaneously",
    "input_schema": {
        "type": "object",
        "properties": {
            "invocations": {
                "type": "array",
                "description": "The tool calls to invoke",
                "items": {
                    "type": "object",
                    "properties": {
                        "name": {
                            "type": "string",
                            "description": "The name of the tool to invoke"
                        },
                        "arguments": {
                            "type": "object",
                            "description": "The arguments to pass to the tool"
                        }
                    }
                }
            }
        }
    }
}
```

```
def run_batch(invocations=[]):
    batch_output = []
    
    for invocation in invocations:
        name = invocation["name"]
        args = json.loads(invocation["arguments"])
        
        tool_output = run_tool(name, args)
        
        batch_output.append({
            "tool_name": name,
            "output": tool_output
        })
    
    return batch_output
```

Once implemented, Claude will be much more likely to use the batch tool when it recognizes that multiple operations can be performed in parallel.

# 2025-08-03

## Tool Use

After Claude requests a tool call, you need to execute the function and send the results back. 

After running the tool function, you need to send the results back to Claude using a tool result block.

The tool result block has several important properties:

- tool_use_id - Must match the id of the ToolUse block that this ToolResult corresponds to
- content - Output from running your tool, serialized as a string
- is_error - True if an error occurred

Each tool call gets a unique ID, and you must match these IDs when sending back results. This ensures Claude knows which result corresponds to which request, even if the results arrive in a different order.

TODO How do we handle tool-call dependencies, e.g., when call #2 consumes call #1's output?

```
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

The complete message history now contains:

- Original user message
- Assistant message with tool use block
- User message with tool result block

OMG, if the user messages is complex with multi tool calls, there will be a huge amount of tokens...

When sending the follow-up request, you must still include the tool schema even though you're not expecting Claude to make another tool call. Claude needs the schema to understand the tool references in your conversation history.

> Claude behaves like a stateless service, keep in mind when designing apps.

Claude will then respond with a final message that incorporates the tool results into a natural response for the user. The tool use workflow is now complete - you've successfully enabled Claude to access real-time information through your custom function.

# 2025-08-02

## Tool use

Tools allow Claude to access information from the outside world, extending its capabilities beyond what it learned during training

Tool use solves this limitation by creating a structured way for Claude to request and receive fresh information.

When a user asks about current weather, you include instructions in your prompt about how to retrieve weather data. Claude recognizes it needs current information and requests weather data for the specific location. Your server then calls a weather API to get real-time conditions and sends that data back to Claude. Finally, Claude combines the fresh weather data with the user's question to provide an accurate, current response.

## Best Practices for Tool Functions

- Use descriptive names: Both your function name and parameter names should clearly indicate their purpose
- Validate inputs: Check that required parameters aren't empty or invalid, and raise errors when they are
- Provide meaningful error messages: Claude can see error messages and might retry the function call with corrected parameters

## Process

- Tool functions: Basically functions like API calls
- Tool schemas: Register your Tools to Claude (TODO different from MCP?)
- Making Tool-Enabled API Calls: To enable Claude to use tools, you need to include a tools parameter in your API call.

## Tool schemas

The complete tool specification has three main parts:

- name - A clear, descriptive name for your tool (like "get_weather")
- description - What the tool does, when to use it, and what it returns
- input_schema - The actual JSON schema describing the function's arguments

You can use Claude to generate tool JSON schemas:

1. give your code
2. ask for generating JSON schema for tool calling
3. add Anthropic doc on tool use
4. generate by following best practices

## Making Tool-Enabled API Calls

```
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

Put schema in the tools, and Claude with check wether it needs tool calls or not. Wehn Claude decides to use a tool, it returns an assistant message with multiple blocks in the content list. Multi-block messages include: Text Block and ToolUse Block.

The ToolUse block includes:

- An ID for tracking the tool call
- The name of the function to call (like "get_current_datetime")
- Input parameters formatted as a dictionary
- The type designation "tool_use"

The tool usage process:

- Send user message with tool schema to Claude
- Receive assistant message with text block and tool use block
- Extract tool information and execute the actual function
- Send tool result back to Claude along with complete conversation history
- Receive final response from Claude

# 2025-08-01

## Prompt Engineering

### Be ing specific 

you can provide clear guidelines or steps that direct Claude toward the kind of output you're looking for.

Two Types of Guidelines:

- List qualities that the output should have
- Provide steps the model should follow

Output Quality Guidelines:

- Length of the response
- Structure and format
- Specific attributes or elements to include
- Tone or style requirements

Process Steps:

- Brainstorm three talents that would create dramatic tension
- Pick the most interesting talent
- Outline a pivotal scene that reveals the talent
- Brainstorm supporting character types that could increase the impact

Example (for a meal planning prompt):

```
Guidelines:
1. Include accurate daily calorie amount
2. Show protein, fat, and carb amounts  
3. Specify when to eat each meal
4. Use only foods that fit restrictions
5. List all portion sizes in grams
6. Keep budget-friendly if mentioned
```

Good prompt needs experts' experience and domain knowledge, this is the key for building AI apps.

Always Use Output Guidelines and Use Process Steps For Complex Problems.

For instance, if you're asking Claude to analyze why a sales team's performance dropped, you'd want to guide it through examining market metrics, industry changes, individual performance, organizational changes, and customer feedback - rather than letting it focus on just one potential cause.

### Structure with XML tags

XML tags provide a simple way to add structure and clarity to your prompts, especially when you're interpolating large amounts of data.

```
Debug my code below ... using provided docs:

from datavortex import xxx

# docs...
```

Better one:

```
Debug my code below ... using provided docs:

<code>
from datavortex import xxx

</code>

<docs>
# docs...
</docs>
```

When to Use XML Tags

- Including large amounts of context or data
- Mixing different types of content (code, documentation, data)
- You want to be extra clear about content boundaries
- Working with complex prompts that interpolate multiple variables

Example:

```
<athlete_information>
- Height: 6'2"
- Weight: 180 lbs
- Goal: Build muscle
- Dietary restrictions: Vegetarian
</athlete_information>

Generate a meal plan based on the athlete information above.
```

### Providing examples

Providing examples in your prompts is one of the most effective prompt engineering techniques you'll use. This approach, known as "one-shot" or "multi-shot" prompting, involves giving Claude sample input/output pairs to guide its responses.

![](https://everpath-course-content.s3-accelerate.amazonaws.com/instructor%2Fa46l9irobhg0f5webscixp0bs%2Fpublic%2F1748623646%2F05_-_005_-_Providing_Examples_04.1748623646206.png)

The improved prompt includes:

- A clear positive example: "Great game tonight!" → "Positive"
- A sarcastic example: "Oh yeah, I really needed a flight delay tonight! Excellent!" → "Negative"
- Context explaining why sarcasm should be treated carefully

Notice how the examples are wrapped in XML tags like <sample_input> and <ideal_output>. This structure makes it crystal clear to Claude what each part represents.

Finding Good Examples from Evaluations and use as examples.

Don't just provide the input/output pair - explain why the output is good will provide additional context to help Claude understand the reasoning.

# 2025-07-31

## Prompt Engineering

Prompt engineering is about taking a prompt you've written and improving it to get more reliable, higher-quality outputs. This process involves iterative refinement - starting with a basic prompt, evaluating its performance, then systematically applying engineering techniques to improve it.

- Set a goal
- Write an initial prompt
- Eval the prompt
- Apply a prompt engineering technique
- Re-eval to verify better performance
- and repeat the last two steps

It is good to build a report for test cases, scores, and results for prompts.

Normally, we should include the following in the report:

- Scenario: background and our goal
- Prompt Inputs: test cases
- Solution Criteria
- Output
- Score: graded by graders
- Reasoning

TODO Are there any tools for prompt engineering? I need to improve my prompt for weekly review. Maybe start with https://langfuse.com/

Found many in <https://mirascope.com/blog/prompt-engineering-tools>, lol. It is way too competitive even for Prompt Engineering...

The first line of your prompt is the most important part of your entire request. This is where you set the stage for everything that follows, and getting it right can dramatically improve your results.

- Clarity and directness: using simple language and avoid ambiguity
  - State what you want explicitly
  - Lead your prompt with a simple statement
  - Instead of: I need to know .... Use: Write three paragraphs about ....
  - Instead of: I was reading about renewable ... Use: Identify three countries that use geothermal energy ...

Direct Instructions

- Use instructions, not questions
- Start with direct action verbs like "Write," "Create," or "Generate"

For example: Generate a one-day meal plan for an athlete that meets their dietary restrictions.

This revision immediately tells Claude:

- What action to take (generate)
- What to create (a meal plan)
- Key constraints (one day, for an athlete, meeting dietary restrictions)

TODO I think it might a good idea to train how to communcate with AI, how to write clear language to AI. Or we could create a new language for AI (like next generation programming language)

TODO create a prompt to improve your prompt and make it clear.

# 2025-07-30

## Prompt evaluation

- Model based grading: use AI model to grade
- Code based grading: Syntax validation
- Human based grading: judged by human

Main aim: 1. build a benchmark first. 2. Keep improving prompts. 3. Find the best Prompt.

Before implementing any grader, you need clear evaluation criteria. For a code generation prompt, you might focus on:

- Format - Should return only Python, JSON, or Regex without explanation
- Valid Syntax - Produced code should have valid syntax
- Task Following - Response should directly address the user's task with accurate code

```
def grade_by_model(test_case, output):
    # Create evaluation prompt
    eval_prompt = """
    You are an expert code reviewer. Evaluate this AI-generated solution.
    
    Task: {task}
    Solution: {solution}
    
    Provide your evaluation as a structured JSON object with:
    - "strengths": An array of 1-3 key strengths
    - "weaknesses": An array of 1-3 key areas for improvement  
    - "reasoning": A concise explanation of your assessment
    - "score": A number between 1-10
    """
    
    messages = []
    add_user_message(messages, eval_prompt)
    add_assistant_message(messages, "```json")
    
    eval_text = chat(messages, stop_sequences=["```"])
    return json.loads(eval_text)
```

TODO create a prompt finder app, to test my prompts

test

# 2025-07-29

## Prompt evaluation

Prompt engineering gives you techniques for writing better prompts, while prompt evaluation helps you measure how well those prompts actually work.

Prompt Engineering: crafting effective prompts
Prompt Evaluation: Automated testing to measure how well your prompts work

Combine both -> Best prompts for your application

We need to build a evaluation pipeline for scoring and improving prompts, handle corner cases from users, because users often provide unexpected inputs.

There will be a new role in the future: Prompt tester.

## Prompt Eval Workflow:

### Draft a Prompt: A basic prompt will serve as baseline for testing and improvement.

```
Please answer the user's question:

{question}
```

### Create an Eval Dataset.

```
"What's 2+2?"
"How do I make oatmeal?"
"How far away is the Moon?"
```

### Feed Through Claude

For example, the first question becomes:

```
Please answer the user's question:
What's 2+2?
```

### Feed Through a Grader

This step provides objective scoring, typically on a scale from 1 to 10, where 10 represents a perfect answer and lower scores indicate room for improvement.

In our example, the grader might assign:
```
Math question: 10 (perfect answer)
Oatmeal question: 4 (needs improvement)
Moon question: 9 (very good answer)
```
The average score across all questions gives you an objective measurement: (10 + 4 + 9) ÷ 3 = 7.66

### Change Prompt and Repeat

```
prompt = f"""
Please answer the user's question:

{question}

Answer the question with ample detail
"""
```

you might get a higher average score of 8.7, indicating that the additional instruction helped Claude provide better responses.

You can also build a version control system for prompt performance.

## Generating test datasets

Use Haiku for cheap and faster datasets generating.

## Running the eval

Load dataset, pass the item to eval function, and get results.

## Model based grading

There are three main approaches to grading model outputs:

- Code graders - Programmatically evaluate outputs using custom logic
- Model graders - Use another AI model to assess the quality
- Human graders - Have people manually review and score outputs

# 2025-07-27

## Streaming

As LLM generate words one by one, we could stream our output. Provide a better and quicker UX.

Claude sends back several types of Stream events:

- MessageStart - A new message is being sent
- ContentBlockStart - Start of a new block containing text, tool use, or other content
- ContentBlockDelta - Chunks of the actual generated text
- ContentBlockStop - The current content block has been completed
- MessageDelta - The current message is complete
- MessageStop - End of information about the current message

there are two powerful techniques for controlling Claude's output: prefilled assistant messages and stop sequences.

instead of just sending a user message, you add an assistant message at the end of your message list. Claude sees this assistant message and thinks "I've already started responding to this question, so I should continue from where I left off."

Prefilled assistant messages provide Claude a lead to continue. Claude will continue the response from there.

For example, if you ask "Is tea or coffee better at breakfast?" without prefilling, Claude typically gives a balanced response mentioning both options. But if you add an assistant message saying "Coffee is better because", Claude will continue from there and build a case for coffee.

The key thing to understand is that Claude continues from exactly where your prefilled text ends. If you write "Coffee is better because", Claude won't repeat that text - it will pick up right after "because" and complete the thought.

Stop sequences force Claude to end its response immediately when it generates a specific string of characters. 

For example, if you ask Claude to "Count from 1 to 10" with a stop sequence of "5", you'll get: 1, 2, 3, 4, 

These techniques are particularly useful for:

- Consistent formatting: Use prefilling to ensure responses always start with a specific structure
- Controlled length: Use stop sequences to cap responses at natural breakpoints
- Biased responses: When you need Claude to take a particular stance rather than being neutral
- Structured output: Combine both techniques to generate responses that fit specific templates

# 2025-07-25

## Coding

今天开始实战，所以我创建的代码不放在笔记了，放在这个仓库里面 <https://github.com/brucexu-eth/claude-courses-code>

重点笔记：

- Anthropic 是 stateless 的，所以如果你要关联上下文，就必须把上下文都提供进去。这也是为什么 claude code 需要时不时 compact 一下，因为每次发送都是整个上下文都发。所以官方提供建议从大往小，然后专注某一个具体的事情，不要同时做
- system prompts 用于设置 role 或者指示 AI 用什么用的姿态来思考和回复
- Temperature 修改选择 token 的概率，从 0 - 1，如果 0 则始终选择最高概率的那个 next word，否则就是更加随机。所以需要在不同的使用场景来选择不一样的，比如 解析数据和处理用 0，脑暴创意用 1

# 2025-07-24

## https://anthropic.skilljar.com/claude-with-the-anthropic-api

看了下课程，似乎是有几个小的 modules 然后组合在一起形成不同的课程，看了下这个应该是最全的，可以从零到一开发整个 AI 项目的，所以先 enroll 了这个课程。

看了下课程信息量好大，感觉这次学完这个就足够了。

### Claude models

- Opus: highest level of intelligence
- Sonnet: balances quality, speed, cost
- Haiku: Most cost-efficient and latency-optimized model (no resoning)

Combine together: use Opus to design the product and Sonnet to code and Haiku for user interact.

### Accessing the API

Parameters:

- API Key
- Model
- Messages
- Max Tokens: Limit to how many "tokens" the model can generate

Process of LLM:

- Tokenization: sentences -> words -> tokens
- Embedding: each token -> (-0.34, 0.87, 0.15 ...) - numbers represent all possible meanings of each words
- Contextualization: adjust embedding based on other embeddings around, because the meaning of the word might be changed in the context. The final embeddings sent to Output Layer to make predict to the next word (highest probability) -> a mix of probability and randomness to select words -> natural and varied responses -> added to the last of the list of embeddings and repeat the process
  - Have we exceeded the max tokens?
  - Did we generate a token matching a stop sequence? -> Model thinks this is the end of the response
  - Did we output an End of Sequence token?
- Generation text

API Response:

- Message
- Usage: Number of input + output tokens
- Stop Reasons: Why the model stopped generation

TODO https://anthropic.skilljar.com/claude-with-the-anthropic-api/287725
<!-- Content_END -->
