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
