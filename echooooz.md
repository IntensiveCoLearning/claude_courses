---
timezone: UTC+8
---

# echo

**GitHub ID:** echooooz

**Telegram:** @echo_zxh

## Self-introduction

一个正常人

## Notes

<!-- Content_START -->
# 2025-08-15

- Claude apps
    - /init to create a claude.md. Type "# xxxx" to add to the file

# 2025-08-13

- mcp server primitives
        - Tools are model-controlled. Claude decides when to call these.
	- Resources are app-controlled. 
	- Prompts are user-controlled.

# 2025-08-12

- MCP
    - MCP, similar to tool use, different in who is authoring it
    - MCP clients - access point to all the tools implemented by the server. Transport agnostic = can communicate over a variety of protocols including. Standard IO/HTTP/Websockets

# 2025-08-11

- Caching
    - save an initial request and reuse the content when you are repeatedly sending the same content. Cheaper & faster to execute
    - rules: initial request will write to the cache, follow up requests can read from the cache, cache lives for 1h
    - have to manually add a 'cache breakpoint' to a block. The content needs to be identical
    - can add cache breakpoints to system prompt & tool definitions other than text blocks, useful when you have a long system prompt or a long list of tools

# 2025-08-10

- combine semantic search &lexical search(bm 25 algo) to improve retrieval accuracy
    - reranking - ask Claude to take a look at user questions, relevant docs, and return the x most relevant ones
    - contextual retrieval - ask Claude to situate/place/add some context to each individual text chunk. If source doc too large, then provide a reduced set of context, say chunk1-3 & chunk 7-8 as context for chunk9

# 2025-08-08

续上次笔记
    - store embeddings in a vector database, embed user query, find closest embeddings(cosine similarity), add related chunks into a prompt

# 2025-08-07

- RAG
    - break the doc up into many chunks & put chunks relevant to the user's question in the prompt. downsides - search/preprocessing/many technical decisions
    - trunking strategies: size based（include overlap thru neighbouring chunks), structure based, semantic based(expensive but more relevant)
    - text embeddings - a numerical representation of the meaning contained in some text

# 2025-08-06

- text editor tool - built in
- web search tool - can restrain Claude to only search a specific domain to avoid random stuff

# 2025-08-05

- batch tool: to increase the chances of Claude making multiple tool calls in a single msg
    - tool calling with streaming 
        - claude will make sure it has generated a valid json by validating a key value pair in isolation before sending back the chunks
        - still irritating with the buffering & validation steps
        - fine-grained tool calling: disables the validation step

# 2025-08-03

- if multi tools    
        - extra functions to take in a list of msg, continue calling Claude until we get back a sign that Claude doesn't want to call a tool anymore
        - stop reason as a sign

# 2025-08-02

- Tool use
    - to set a reminder
        - get the current date time
        - add duration to date time 
        - set a reminder
    - steps of a tool use
        - 1/ write a tool function
        - 2/ write a JSON schema
        - 3/ call Claude with JSON schema. helps Claude understand how to call the tool。 Gets multi-block msg from Claude - content block & tool use block
        - 4/ run the tool
        - 5/ add tool use(a new block) and call Claude again(too use ids: to tie tool requests to tool results)
    - overview of the entire process  一个用户端发起的简单问题，可能包含有多轮对话，其中包含用户端的执行和结果发送
        - server sends tool schema & user msg
        - claude sends assistant msg incl. text block & tooluse block(request to call a tool)
        - server runs the tool function and sends a user msg with the result of function
        - claude sends back the final text block with the result

# 2025-08-01

- 今天学习较少，主要研究模型计费
    - CC新会话的input token就有19.6k。openai和gemini都解释说是因为agent system prompt巨大，但是看不到？
    - 每轮对话，input token都会累计，input token数量巨大，占主要收费占比
    - CC context window上限20w input token，到上限了会auto compact。Gemini支持100w token上下文

# 2025-07-31

- Prompt engineering
    - simple language & instructions instead of question((s(use action verbs - identify/generate/write)
    - specific - provide a list of qualities(highly recommend), or steps(with troubleshooting hard problems )
    - structure with XML tags, useful when incl. a lot of context. eg. <my_code> ... </my_code>
    - give Claude sample input/output pairs

# 2025-07-29

- Prompt evaluation
    - Prompt draft recommendation - run the prompt thru an evaluation pipeline to score it, en iterate on the prompt
    - process: 1/draft a prompt 2/create an eval dataset 3/feed thru claude 4/feed thru a grader
        - model based grading - task following. write a prompt to ask a model for strengths, weaknesses, reasoning besideds score, to get a more concrete score
        - code based grading - format/syntax
        - human based grading

# 2025-07-28

- Claude Code other tips
    - Custom commands: create file-name.md to add a custome commands; can take arguments 
    - claude mcp add playwright npx @playwright/mcp@latest, ask CC to review using the browser mcp and improve it
    - github integration: use CC as a team member that can use tools with mcp servers - https://anthropic.skilljar.com/claude-code-in-action/303240

- Hooks & the SDK
    - Hooks give the ability to execute code before/after the tool execution. eg: to prevent Claude from reading .env
    - Useful Hooks
        - run the ts type checker anytime we edit a ts file
        - launch a separate copy of Claude Code to research the queries dir and see if similar query already exists. to avoit duplicating codebase

# 2025-07-26

- Claude Code - Context Controlling
    - /init - to scan the codebase and write a summary into Claude.md
    - @ specific files and add them to Claude.md
    - ctrl+v to paste images
        - enhance intelligence: 1/ shift + tab twice to enable plan mode 2/ its a tough task so ultrathink about the best way
    - hit escape once to interrupt Claude, and add memory with "#""
    - hit escape twice to revert to a previous mes, removes context not relevant to the current task
    - /compact
    - /clear

# 2025-07-25

- CC连vertex ai坑多，不推荐
    - Claude Code 默认依赖 Application Default Credentials（ADC）才能访问 Vertex AI。登录必须用gcloud auth application-default login而不是gcloud auth login 
    - 保证ADC有效后再跑export CLAUDE_CODE_USE_VERTEX=1
    export CLOUD_ML_REGION=us-east5
    export ANTHROPIC_VERTEX_PROJECT_ID=YOUR_PROJECT_ID    
    - 配置都过关，python能跑通，但CC仍然出错，一直运行不返回内容知道timeout。已知bug。
- debug logs: https://chatgpt.com/share/688341c8-a0ac-800e-9df8-e5900c769b71

# 2025-07-24

- The anthropic API & claude do not store any messages.
- To have a conversation:
    - manually maintain a list of msg in your code
    - provide that list of msg with each follow up request
- Temperature
    - Low: deterministic
    - High: random
- Controlling Claude's output
    - prefilled assistant msg
    - stop sentences


<!-- Content_END -->
