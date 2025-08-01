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
