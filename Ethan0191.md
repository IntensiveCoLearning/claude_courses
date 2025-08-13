---
timezone: UTC+8
---

# Ethan

**GitHub ID:** Ethan0191

**Telegram:** @Ethan0191

## Self-introduction

beginner

## Notes

<!-- Content_START -->
# 2025-08-13

* 将工具调用与流式输出结合，可在模型生成工具参数的同时向用户实时更新，显著提升交互响应性。
* 事件类型：

  * 文本流：`ContentBlockDelta`（常规文本增量）。
  * 工具参数流：`InputJsonEvent`，含

    * `partial_json`：最新到达的一段 JSON 片段；
    * `snapshot`：到目前为止累计起来的整段 JSON 文本（所见即所得快照）。
* 默认模式（带 JSON 校验）：API 会先缓冲，等**完整的顶层 key–value** 生成并通过 schema 校验，再一次性发送该字段对应的增量；因此体验上常见“**先等一会→再突发一段**”。此模式下 `snapshot` 始终是**合法 JSON**，前后端处理更稳。优化思路：把超大内容拆成多个顶层键或数组元素，让更多“小块”更快通过校验并流出。
* 细粒度工具调用（`fine_grained=True`）：关闭 API 端 JSON 校验，Claude 一生成就下发，**无顶层键间的缓冲**，更接近“真正实时”的流式行为；但你必须在客户端处理**可能不合法的 JSON**（如 `undefined`、未闭合对象等），对解析/合并/回退做健壮性设计。
* 适用场景取舍：

  * 需要**实时进度**可视化、**尽早处理**部分结果、默认缓冲显著影响体验，且你能做好容错 → 选细粒度。
  * 绝大多数应用默认模式已足够稳定省心 → 保持验证开启。
* 处理不合法 JSON 的基本做法：

  * 增量拼接并宽松解析，待形成可解析的 JSON 再下游处理；必要时加超时与回退。

```python
# 监听流
for chunk in stream:
    if chunk.type == "input_json":
        # 实时观测片段
        print(chunk.partial_json)
        # 使用当前累计快照
        current_args = chunk.snapshot
```

```python
# 细粒度模式下的健壮解析
import json
try:
    args = json.loads(current_args)  # snapshot 可能暂时不合法
except json.JSONDecodeError:
    # 记录并等待更多片段或走回退逻辑
    log_warning("Invalid JSON snapshot; will retry on next chunk.")
```

* 工程实践要点：

  * **Schema 即性能**：避免把巨量文本塞进同一顶层字段；改用多个键/数组项以缩短“等待完整键”的时间。
  * 在工具参数里加入 `step_id`/`field` 等标识，便于把增量与 UI 匹配。
  * 保留事件流做回放与对账；对下游工具/第三方 API 做限流、超时与重试策略，避免单点拖慢整体体验。
* 一句话：\*\*默认（有校验）\*\*更稳更省心但粒度偏粗；\*\*细粒度（无校验）\*\*更快更实时但要自己兜底 JSON 与错误处理。根据实时性诉求与工程容错能力选择其一或组合使用。

# 2025-08-12

工具的调用太实用了，但记得在chat函数里面调用tool_choice。

# 2025-08-11

在 Claude 里用 batch_tool 并行调用工具的核心好处是更低延迟、更高吞吐、更省 token/费用、以及一次性拿到一致性的结果。官方文档把它称为把多次工具调用“打包并行”的做法（亦可直接用 Claude 的“并行工具调用/Token-efficient tool use”特性）。        
1. 提供一个 batch_tool 元工具：输入为 invocations[]（每项含工具名和参数），鼓励模型一次提出所有并行调用；后端把这些子调用真正并发执行并汇总结果。    
2. 启用官方并行工具调用：直接让 Claude 在单条 assistant 消息里并列多个 tool_use，你在下一条 user 消息里逐个回填 tool_result。

# 2025-08-09

今天学的可太难了，辅助函数还有多工具调用，但是我对assitent_message里面多内容还是有点陌生，不知道他这一步是用来做什么，为什么user_message里面就没有那么多block。通过检查remainder以及辅助函数就可以实现多工具调用，还是挺有意思的。  
PS今天开始正式独库公路骑行，今天的课程实在克孜利亚大峡谷景区学的[doge]
![180bdf22277112b09a61adf0e43cb73](https://github.com/user-attachments/assets/efbfc18c-71a1-4fd9-9b4a-9795db1d53ee)


# 2025-08-08

今天学的是Tool use with Claude前五节，输出   
[{'role': 'user', 'content': 'What is the exact time, formatted as HH:MM:SS?'},
 {'role': 'assistant',
  'content': [TextBlock(citations=None, text='I can get the current time in the HH:MM:SS format for you. Let me do that:', type='text'),
   ToolUseBlock(id='toolu_01Qimc7DSYsLXP9izLAAbRg4', input={'date_format': '%H:%M:%S'}, name='get_current_datetime', type='tool_use')]}]  
这样能做claude之外更多的事情。

# 2025-08-07

今天完成了Prompt engineering techniques剩余章节并通过考试，说白了就是问claude要答案的时候能比较明确给出好的模板最好，如果不能就告诉他怎么思考。

# 2025-08-06

使用XML 或其他结构化标签（如JSON 或YAML）有助于系统性地理解和处理数据。结构化标签为数据提供了明确的框架和层次，使得机器可以更容易地解析、验证和操作数据。﻿  
除了XML，还有其他一些常用的结构化标签，例如:﻿  
JSON (JavaScript Object Notation):一种轻量级的数据交换格式，常用于Web应用程序中。  
YAML (YAML Ain't Markup Language):一种可读性很强的数据序列化格式，常用于配置文件。

# 2025-08-05

如何提问？  
第一个是输出质量指南包括：长度、格式、元素和风格。  
第二个把包括头脑风暴、选其一、深度刻画和丰富环境。

# 2025-08-04

今天把迭代改进过程学习了一下，根据目标完成的编写他亦是，评估后不断重复提示和重新评估，最终得到改进后。实验时使用 max_concurrent_tasks 参数控制并发性，越高越快。重点还是要不断迭代。

# 2025-08-03
为了提升提示词的丰富度
1.在数据集生成阶段提出 solution_criteria  
2.直接再grade_by_model内使用  
~~~~def generate_dataset():
    prompt = """
Generate an evaluation dataset for a prompt evaluation. The dataset will be used to evaluate prompts that generate Python, JSON, 
or Regex specifically for AWS-related tasks. Generate an array of JSON objects, each representing task that requires Python, JSON, or a Regex to complete.

Example output:
```json
[
  {
    "task": "Description of task",
    "format": "python"  # or "json" or "regex"
    "solution_criteria":"Key criteria for a good solution, e.g., 'correctness', 'efficiency', 'clarity'"
  },
  ...additional
]
```
*Focus on tasks that can be solved by writing a single Python function, a single JSON object, or a single regex
*Focus on tasks that do not require writing much code
Please generate 3 objects.

"""
    messages = []
    add_user_message(messages, prompt)
    add_assistant_message(messages, "```json")
    text = chat(messages, stop_sequences=["```"])
    return json.loads(text)
~~~~
You have passed! the quiz on prompt evaluation
~~~~
You want to measure how well your prompts actually work in practice. Which approach should you focus on?  
answer：Prompt evaluation methods（提示评估方法）

beacuse：要“衡量实际效果”，核心是系统化评测——定义客观指标、构建测试集、离线评测 + 线上 A/B、稳定性（多随机种子）与安全红队、误差分析与回归门禁。
~~~~

# 2025-08-02

``````
test for the markdown
``````
add funtions to validate JSON/Python/regex  
make sure the format key  
make sure the draft prompt  
merge the model grader and the code grader   
Average score: 4.83

# 2025-07-30

数据集的生成：为AWS编写三种特定类型的输出：Python代码、JASO配置文件和表达式。
写jason的常见用法
import json

data = {"name": "Alice", "age": 30}
with open('dataset.json', 'w', encoding='utf-8') as f:
    json.dump(data, f, ensure_ascii=False, indent=2)  # 写入并美化缩进


# 2025-07-29

typical eval workflow：提出问题，创建数据集，通过claude进行反馈，通过打分机制，实现提问迭代。

# 2025-07-27

Prompts engineering  is a toolkit for crafting effective prompts. 给提示打分，通过迭代这样可以得到更好的提示可靠性。

# 2025-07-26

temperature、response streaming、controling model output、structured data

# 2025-07-25

System prompts， temperature and response streaming.

# 2025-07-24

#'Quantum computing is a revolutionary computing paradigm that uses quantum mechanical phenomena like superposition and entanglement to process information in ways that can potentially solve certain problems exponentially faster than classical computers.'


<!-- Content_END -->
