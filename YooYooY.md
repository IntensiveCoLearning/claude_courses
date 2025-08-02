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
# 2025-08-02

# Model based grading

### Types of Graders

- Code graders: Programmatically evaluate outputs using custom logic
- Model graders: Use another AI model to assess the quality
- Human graders: Have people manually review and score outputs

### Implementing a Model Grader

```python
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

# Code based grading

- Format: The response should return only the requested code type (Python, JSON, or Regex) without explanations
- Valid Syntax: The generated code should actually parse correctly as the intended language
- Task Following: The response should directly address what was asked and be accurate

### Syntax Validation Functions

```python
def validate_json(text):
    try:
        json.loads(text.strip())
        return 10
    except json.JSONDecodeError:
        return 0

def validate_python(text):
    try:
        ast.parse(text.strip())
        return 10
    except SyntaxError:
        return 0

def validate_regex(text):
    try:
        re.compile(text.strip())
        return 10
    except re.error:
        return 0
```

> For the code grader to know which validator to use, your test cases need to specify the expected output format:

```python
{
    "task": "Create a Python function to validate an AWS IAM username",
    "format": "python"
}
```

### Combining Scores

```python
model_grade = grade_by_model(test_case, output)
model_score = model_grade["score"]
syntax_score = grade_syntax(output, test_case)

score = (model_score + syntax_score) / 2
```

## Exercise Task

Give the Model Grader more context on what a good solution looks like

```python
# Function to grade a test case + output using a model
def grade_by_model(test_case, output):
    eval_prompt = f"""
You are an expert AWS code reviewer. Your task is to evaluate the following AI-generated solution.

Original Task:
<task>
{test_case["task"]}
</task>

Solution to Evaluate:
<solution>
{output}
</solution>

Criteria you should use to evaluate the solution:
<criteria>
{test_case["solution_criteria"]}
</criteria>

Output Format
Provide your evaluation as a structured JSON object with the following fields, in this specific order:
- "strengths": An array of 1-3 key strengths
- "weaknesses": An array of 1-3 key areas for improvement
- "reasoning": A concise explanation of your overall assessment
- "score": A number between 1-10

Respond with JSON. Keep your response concise and direct.
Example response shape:
{{
    "strengths": string[],
    "weaknesses": string[],
    "reasoning": string,
    "score": number
}}
    """

    messages = []
    add_user_message(messages, eval_prompt)
    add_assistant_message(messages, "```json")
    eval_text = chat(messages, stop_sequences=["```"])
    return json.loads(eval_text)
```

```python
# Functions to validate the output structure
import re
import ast


def validate_json(text):
    try:
        json.loads(text.strip())
        return 10
    except json.JSONDecodeError:
        return 0


def validate_python(text):
    try:
        ast.parse(text.strip())
        return 10
    except SyntaxError:
        return 0


def validate_regex(text):
    try:
        re.compile(text.strip())
        return 10
    except re.error:
        return 0


def grade_syntax(response, test_case):
    format = test_case["format"]
    if format == "json":
        return validate_json(response)
    elif format == "python":
        return validate_python(response)
    else:
        return validate_regex(response)
```

```python
# Function to execute a single test case and grade the output
def run_test_case(test_case):
    """Calls run_prompt, then grades the result"""
    output = run_prompt(test_case)

    model_grade = grade_by_model(test_case, output)
    model_score = model_grade["score"]
    reasoning = model_grade["reasoning"]

    syntax_score = grade_syntax(output, test_case)

    score = (model_score + syntax_score) / 2

    return {
        "output": output,
        "test_case": test_case,
        "score": score,
        "reasoning": reasoning,
    }
```

# 2025-08-01

今天账号被封了，很扎心，折腾了用代理，找中间商，都不是很满意，最后把接口换成了[openrouter](https://openrouter.ai)，发现还不错，里面统一做了接口的整合，还省去了很多适配工作。

https://openrouter.ai/anthropic/claude-sonnet-4/api 统一封装成OpenAI格式:

```python
from openai import OpenAI

client = OpenAI(
  base_url="https://openrouter.ai/api/v1",
  api_key="<OPENROUTER_API_KEY>",
)

completion = client.chat.completions.create(
  extra_headers={
    "HTTP-Referer": "<YOUR_SITE_URL>", # Optional. Site URL for rankings on openrouter.ai.
    "X-Title": "<YOUR_SITE_NAME>", # Optional. Site title for rankings on openrouter.ai.
  },
  extra_body={},
  model="anthropic/claude-sonnet-4",
  messages=[
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "What is in this image?"
        },
        {
          "type": "image_url",
          "image_url": {
            "url": "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Gfp-wisconsin-madison-the-nature-boardwalk.jpg/2560px-Gfp-wisconsin-madison-the-nature-boardwalk.jpg"
          }
        }
      ]
    }
  ]
)
print(completion.choices[0].message.content)
```

# 2025-07-31

# Running the eval

The run_prompt Function:

```python
def run_prompt(test_case):
    """Merges the prompt and test case input, then returns the result"""
    prompt = f"""
Please solve the following task:

{test_case["task"]}
"""
    
    messages = []
    add_user_message(messages, prompt)
    output = chat(messages)
    return output
```

The run_test_case Function

```python
def run_test_case(test_case):
    """Calls run_prompt, then grades the result"""
    output = run_prompt(test_case)
    
    # TODO - Grading
    score = 10
    
    return {
        "output": output,
        "test_case": test_case,
        "score": score
    }
```

The run_eval Function

```python
def run_eval(dataset):
    """Loads the dataset and calls run_test_case with each case"""
    results = []
    
    for test_case in dataset:
        result = run_test_case(test_case)
        results.append(result)
    
    return results
```

Running the Evaluation

```python
with open("dataset.json", "r") as f:
    dataset = json.load(f)

results = run_eval(dataset)

print(json.dumps(results, indent=2))
```

Each result contains three key pieces of information:

output: The complete response from Claude
test_case: The original test case that was processed
score: The evaluation score (currently hardcoded)

# 2025-07-30

# Generating test datasets

**关键要求：**

* 返回的结果必须是**干净的纯格式输出**，不包含说明、标题或页脚。

**初始 Prompt 模板（版本1）：**

```python
prompt = f"""
Please provide a solution to the following task:
{task}
"""
```

---

#### 构建评估数据集（Creating an Evaluation Dataset）

* 每个输入组合将与 Prompt 一起运行，用于分析效果。
* 数据集格式为 JSON 数组，每个元素为一个任务对象：

```json
[
  {
    "task": "..."
  },
  ...
]
```

* 数据可以手工创建，也可以通过模型自动生成。

---

#### 使用 Claude 模型自动生成测试数据（Generating Test Data with Code）

推荐使用轻量模型（如 Haiku）生成测试数据。

##### Claude 交互辅助函数：

```python
def add_user_message(messages, text):
    user_message = {"role": "user", "content": text}
    messages.append(user_message)

def add_assistant_message(messages, text):
    assistant_message = {"role": "assistant", "content": text}
    messages.append(assistant_message)

def chat(messages, system=None, temperature=1.0, stop_sequences=[]):
    params = {
        "model": model,
        "max_tokens": 1000,
        "messages": messages,
        "temperature": temperature
    }
    if system:
        params["system"] = system
    if stop_sequences:
        params["stop_sequences"] = stop_sequences
    
    response = client.messages.create(**params)
    return response.content[0].text
```

---

#### 生成数据集函数 `generate_dataset()`

```python
def generate_dataset():
    prompt = """
Generate an evaluation dataset for a prompt evaluation. The dataset will be used to evaluate prompts that generate Python, JSON, or Regex specifically for AWS-related tasks. Generate an array of JSON objects, each representing task that requires Python, JSON, or a Regex to complete.

Example output:
json:
[
  {
    "task": "Description of task",
  },
  ...additional
]


* Focus on tasks that can be solved by writing a single Python function, a single JSON object, or a single regex
* Focus on tasks that do not require writing much code

Please generate 3 objects.
"""

messages = []
add_user_message(messages, prompt)
add_assistant_message(messages, "```json")
text = chat(messages, stop_sequences=["```"])
return json.loads(text)
```


---

#### 测试数据生成（Testing the Dataset Generation）

```python
dataset = generate_dataset()
print(dataset)
````

应返回 3 个任务，分别用于测试 Python、JSON 和 Regex 类型的输出。

---

#### 保存数据集（Saving the Dataset）

```python
with open('dataset.json', 'w') as f:
    json.dump(dataset, f, indent=2)
```

文件保存为 `dataset.json`，可在后续 prompt 评估中直接加载。

# 2025-07-29

## Prompt evaluation

### Summary

When working with Claude, writing a good prompt is just the beginning. To build reliable AI applications, you need to understand two critical concepts: prompt engineering and prompt evaluation. Prompt engineering gives you techniques for writing better prompts, while prompt evaluation helps you measure how well those prompts actually work.

### Prompt Engineering vs Prompt Evaluation

Prompt engineering is your toolkit for crafting effective prompts. It includes techniques like:

- Multishot prompting
- Structuring with XML tags
- Many other best practices

These techniques help Claude understand exactly what you're asking for and how you want it to respond.

Prompt evaluation takes a different approach. Instead of focusing on how to write prompts, it's about measuring their effectiveness through automated testing. You can:

- Test against expected answers
- Compare different versions of the same prompt
- Review outputs for errors

### Three Paths After Writing a Prompt

Once you've drafted a prompt, you typically face three options for what to do next:

Option 1: Test the prompt once and decide it's good enough. This carries a significant risk of breaking in production when users provide unexpected inputs.

Option 2: Test the prompt a few times and tweak it to handle a corner case or two. While better than option 1, users will often provide very unexpected outputs that you haven't considered.

Option 3: Run the prompt through an evaluation pipeline to score it, then iterate on the prompt based on objective metrics. This approach requires more work and cost, but gives you much more confidence in your prompt's reliability.

### Why Most Engineers Fall Into Testing Traps

Options 1 and 2 are common traps that all engineers fall into, myself included. It's natural to write a prompt for a serious application and not test it thoroughly enough. We tend to underestimate how many edge cases real users will encounter.

The reality is that when you deploy a prompt to production, users will interact with it in ways you never anticipated. What seemed like a solid prompt during your limited testing can quickly break down when faced with the full variety of real-world inputs.

### The Evaluation-First Approach

Option 3 represents a more systematic approach to prompt development. By running your prompt through an evaluation pipeline, you get objective metrics about its performance across a broader range of test cases. This data-driven approach lets you:

- Identify weaknesses before they become production issues
- Compare different prompt versions objectively
- Iterate with confidence based on measurable improvements
- Build more reliable AI applications

While this approach requires more upfront investment in time and testing infrastructure, it pays dividends in the reliability and robustness of your final application. The goal is to catch problems during development rather than after your users encounter them.


### A typical eval workflow

- Step 1: Draft a Prompt

```python
prompt = f"""
Please answer the user's question:

{question}
"""
```

- Step 2: Create an Eval Dataset

	- Contains questions that we will merge with our prompt
	- Can be assembled by hand or generated by Claude

- Step 3: Feed Through Claude

	> Take each question from your dataset and merge it with your prompt template to create complete prompts. Then send each one to Claude to get responses.

	For example, the first question becomes:
	`
	Please answer the user's question:
	What's 2+2
	`

- Step 4: Feed Through a Grader

	> This step provides objective scoring, typically on a scale from 1 to 10, where 10 represents a perfect answer and lower scores indicate room for improvement.

- Step 5: Change Prompt and Repeat

	> add more guidance to your prompt:
```python
prompt = f"""
Please answer the user's question:

{question}

Answer the question with ample detail
"""
```

#### Prompt Scoring

- Get an objective measurement of the output of each prompt
- Use the version with the best score, or iterate again

# 2025-07-28

### Structured Data

Structured Data Generation = technique using assistant message prefilling + stop sequences to get raw output without Claude's natural explanatory headers/footers.

Problem = Claude automatically adds markdown formatting, headers, commentary when generating JSON/code/structured content. Users often want just the raw data for copy/paste functionality.

Solution Pattern:
1. User message = request for structured data
2. Assistant message prefill = opening delimiter (e.g., "\`\`\`json")  
3. Stop sequence = closing delimiter (e.g., "\`\`\`")

How it works = Claude sees prefilled message, assumes it already started response, generates only the requested content, stops when hitting delimiter.

Result = Raw structured data output with no extra formatting or commentary.

Application = Works for any structured data type (JSON, Python code, lists, etc.), not just JSON. Use whenever you need clean, parseable output without explanatory text.

Key benefit = Output can be directly used/copied without manual selection or parsing of unwanted text.


#### Assistant Message Prefilling + Stop Sequences

```python
messages = []

add_user_message(messages, "Generate a very short event bridge rule as json")
add_assistant_message(messages, "```json")

text = chat(messages, stop_sequences=["```"])
# text.strip()
```

#### Processing the Response

```python
import json

# Clean up and parse the JSON
clean_json = json.loads(text.strip())
```

### Structured Data Exercise

```python
messages = []
prompt = """
Generate three different sample AWS CLI commands. Each should be very short.
"""
add_user_message(messages, prompt)
add_assistant_message(messages, "Here are all three commands in a single block without any comments: \n```bash")

text = chat(messages, stop_sequences=["```"])
text.strip()
```

# 2025-07-27

## Claude Response Streaming 学习笔记

### 一 背景与问题

* Claude 在生成响应时可能需要 **10-30 秒**，用户这段时间只能看到 loading 提示，体验不佳。
* 传统 API 模式下，服务端需要等 Claude 全部生成完响应，才能返回给前端用户，导致明显延迟。

---

### 二 解决方案：响应流（Response Streaming）

* Claude 支持 **分块返回响应文本**（streaming），使得用户能看到实时生成的内容，显著提升交互体验。
* 响应以 **事件流（events）** 的形式返回，每个事件包含一部分内容。

---

### 三、⚙️ Streaming 的事件类型解析

当启用流式响应时，Claude 会返回以下几类事件：

| 事件名称                | 说明              |
| ------------------- | --------------- |
| `MessageStart`      | 开始发送新消息         |
| `ContentBlockStart` | 开始新的内容块（文本、工具等） |
| `ContentBlockDelta` | 正文内容块的一部分（核心内容） |
| `ContentBlockStop`  | 当前内容块结束         |
| `MessageDelta`      | 当前消息已完成         |
| `MessageStop`       | 整个消息流结束         |

*最重要的是：`ContentBlockDelta` —— 包含了生成的实际文本内容。*

---

### 四、 基本代码示例（启用 streaming）

```python
messages = []
add_user_message(messages, "Write a 1 sentence description of a fake database")

stream = client.messages.create(
    model=model,
    max_tokens=1000,
    messages=messages,
    stream=True  # 关键参数
)

for event in stream:
    print(event)  # 实时输出事件内容
```

---

### 五、 简化版本：只提取文本内容（推荐方式）

```python
with client.messages.stream(
    model=model,
    max_tokens=1000,
    messages=messages
) as stream:
    for text in stream.text_stream:
        print(text, end="")  # 实时展示纯文本
```

* SDK 提供了 `text_stream` 简化接口，只提取文字部分，避免手动解析事件。

---

### 六、 获取完整消息（适合存储或后处理）

```python
with client.messages.stream(
    model=model,
    max_tokens=1000,
    messages=messages
) as stream:
    for text in stream.text_stream:
        pass  # 实时发送给前端显示
    
    final_message = stream.get_final_message()  # 获取完整内容保存数据库
```

**总结**：

* `text_stream`：用于前端展示。
* `get_final_message()`：用于数据持久化或进一步处理。


以下是对文章《**Controlling model output**》的笔记总结，清晰地整理了如何控制 Claude 输出的两种高级方法，适合用于 Claude API 实践或提示词设计优化：

---

## Claude 输出控制技巧学习笔记

### 一、 核心目标

除了写好 prompt，还可以通过 **两种方法精准控制 Claude 的输出**：

1. **预填充助手消息（Prefilled Assistant Messages）**
2. **停止序列（Stop Sequences）**

这两种方式可以控制 Claude **从哪里开始输出**，以及**在哪里停止输出**。

---

### 二、 技巧一：预填充助手消息（Prefilled Assistant Messages）

#### 作用

通过先添加一段“助手消息”的开头内容，引导 Claude 从指定位置 **继续补全**。

#### 原理

Claude 会认为自己“已经开始了回复”，于是从这个助手消息的结尾继续生成内容。

#### 示例

```python
messages = []
add_user_message(messages, "Is tea or coffee better at breakfast?")
add_assistant_message(messages, "Coffee is better because")
answer = chat(messages)
```

Claude 的回复将是：

```
Coffee is better because it contains more caffeine and provides a quicker energy boost...
```

#### 应用方式

| 想要 Claude 偏向哪一边 | 填写的 Assistant Message            |
| --------------- | -------------------------------- |
| 偏向咖啡            | `"Coffee is better because"`     |
| 偏向茶             | `"Tea is better because"`        |
| 持否定立场           | `"Neither is very good because"` |

####  提醒

Claude **不会重复**你预填的内容，它会从那之后继续写。

---

### 三、技巧二：停止序列（Stop Sequences）

#### 作用

当 Claude 生成了某些字符串时，**立即停止输出**，防止继续生成无关内容。

#### 原理

设置 stop\_sequences 参数为一个字符串列表，只要 Claude 输出到这些字符串之一，就会 **立刻中断**生成，且不会显示这些字符串。

#### 示例

```python
messages = []
add_user_message(messages, "Count from 1 to 10")
answer = chat(messages, stop_sequences=["5"])
```

输出结果：

```
1, 2, 3, 4,
```

Claude 在生成 “5” 前停止，"5" 不会出现在结果中。

#### 细节技巧

为了避免停在错误位置，可以更具体地设置：

* `stop_sequences=["5"]` 可能意外截断句子
* `stop_sequences=[", 5"]` 更精准

---

### 四、 实用场景

| 场景         | 建议使用技巧  |
| ---------- | ------- |
| 保持输出格式一致   | 预填充助手消息 |
| 限制输出长度     | 停止序列    |
| 引导偏向性立场    | 预填充助手消息 |
| 按模板生成结构化内容 | 两者结合使用  |

---

### 五、 总结

* **Prefilled Assistant Messages**：控制输出起点，引导方向和格式。
* **Stop Sequences**：控制输出终点，防止生成过长或不需要的内容。

它们是构建 **可控、稳定、高质量** AI 应用的关键工具。

# 2025-07-26

### Chat exercise

```python
messages = []

while True:
	# Get user input
	user_input = input("> ")
	print(">", user_input)

	#Add user input to the list of message
	add_user_message(messages, user_input)
	# Call Claude with the 'chat' function
	answer = chat(messages)
	# Add generated text to the list of messages
	add_assistant_message(messages, answer)
	# Print
	print("---")
	print(answer)
	print("---")
```

### System prompts exercise

```python
messages = []

add_user_message(
	messages,
	"Write a Python function that checks a string for duplicate characters".
	)

# answer = chat(messages)
answer = chat(messages, system = "You are a Python engineer who writes very concise code")

answer
```

### Implementing Temperature in Code

```python
def chat(messages, system=None, temperature=1.0):
    params = {
        "model": model,
        "max_tokens": 1000,
        "messages": messages,
        "temperature": temperature
    }
    
    if system:
        params["system"] = system
    
    message = client.messages.create(**params)
    return message.content[0].text
```

Testing Temperature Effects

```python
# Low temperature - more predictable
answer = chat(messages, temperature=0.0)

# High temperature - more creative  
answer = chat(messages, temperature=1.0)
```

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
