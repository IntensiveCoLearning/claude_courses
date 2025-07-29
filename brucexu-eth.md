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
