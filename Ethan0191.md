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
