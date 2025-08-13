---
timezone: UTC+9
---

# hython

**GitHub ID:** joyc

**Telegram:** @hython

## Self-introduction

web3 从业者，AI 爱好者

## Notes

<!-- Content_START -->
# 2025-08-13

# 第29课：Tool Schemas

## 1. JSON Schema 基本概念
- **定义**：JSON Schema 是一种通用的数据验证规范，广泛应用于验证 JSON 数据结构。它本身与语言模型或工具调用无直接绑定，但在工具调用中非常实用。
- **用途**：在工具调用场景下，JSON Schema 用于详细描述工具可用的参数、参数类型及其含义，帮助 Claude 等模型正确理解并调用工具。
- **优势**：标准化、成熟且被广泛支持，易于与各类模型和系统集成。

## 2. Tool Schema 结构
- 一个完整的工具规范包含三个主要部分：
  1. **工具名称 (name)**  
     - 唯一标识工具，如 `get_weather`，便于管理和调用。
  2. **工具描述 (description)**  
     - 用 3-4 句清晰描述工具的功能、适用场景和返回内容，便于模型理解用途，并详细说明每个参数的作用。
     - 最佳实践包括：具体说明工具做什么、何时使用、返回什么数据，以及各参数的详细说明。
  3. **输入模式 (input_schema)**  
     - 按照 JSON Schema 规范定义工具参数。
     - 每个参数需包含：
       - `type`（数据类型，如 `string`、`number` 等）
       - `description`（建议 3-4 句，详解参数作用及影响）

## 3. 编写 Tool Schema 的技巧
- 复制你的工具函数代码（如 `get_current_datetime`）。
- 在 Claude 窗口中输入函数代码，并请求其依据 Anthropic API 文档和最佳实践生成标准 JSON Schema。
- Claude 会自动生成规范且易用的 Schema，可直接复制到项目代码中使用。

## 4. 命名规范与类型提示
- 建议采用工具函数名与 Schema 变量名对应的命名方式，例如：  
  - 工具函数：`get_current_datetime`  
  - Schema 变量：`get_current_datetime_schema`
- 可选地导入并使用 `ToolParam` 包装 Schema，提升类型安全性：  
  ```python
  from anthropic.types import ToolParam
  get_current_datetime_schema = ToolParam({...})
  ```
  - 虽非强制，但有助于防止类型错误，提高代码健壮性。

## 5. 最佳实践
- 工具和参数描述务必具体、详尽，避免过于简略。
- 参数说明应清晰明了，让模型能够准确理解并调用工具。
- 使用统一命名风格，方便管理多个工具及其 Schema。

# 2025-08-11

# 第27课：项目概览

## 项目目标
- 实现一个能让 Claude 设定未来提醒的 Jupyter Notebook 项目。
- 用户示例输入：`设置一个医生预约的提醒，在下周四`。
- Claude 预期输出：`好的，我会在那个时间提醒你`。

## 主要挑战
1. **Claude 虽能获取当前日期，但并不总能准确获取当前时间**。
2. **Claude 的时间加法计算并不总是正确**（如计算几百天后的日期可能出错）。
3. **Claude 无法直接设置提醒**，缺乏执行机制。

## 解决方案
- 为每个问题分别实现一个工具（Tool），共三个：
    1. **获取当前日期时间工具**：返回当前日期和时间。
    2. **日期时间加法工具**：在指定日期时间上加上一个时间间隔，返回新日期时间。
    3. **提醒设置工具**：在指定时间创建提醒。

## 实施方式
- 分步实现工具，从第一个简单工具开始。
- 逐步组合工具以实现完整的提醒功能。
---
# 第28课：为 Claude 构建 “获取当前日期时间” 工具函数

本笔记整合了课程页面与视频文字稿的要点，帮助你快速回顾实现第一个工具函数（get_current_datetime）的全过程。

1. 工具函数（Tool Functions）的概念
   在构建 AI 应用时，Claude 需要查询实时信息或执行动作，可通过工具函数完成。它们是普通的 Python 函数，当 Claude 判断需要额外数据时会被自动调用。  
   示例场景：用户问 “现在几点？”，Claude 会调用获取时间的工具函数。 

2. 编写工具函数的最佳实践
     • 命名清晰：函数名与参数名应清楚表意，例如 ‎⁠get_current_datetime(date_format)⁠。
     • 校验输入：检查必要参数是否为空或无效，及时抛出异常。
     • 错误信息友好：抛出明确的 ‎⁠ValueError⁠，便于 Claude 读取并尝试纠正调用。
这些做法让函数更健壮，也利于 Claude 在首次调用失败后自动重试。 

3. 步骤拆解：实现 get_current_datetime
 1. 创建新 Notebook：课程附带 ‎⁠001Tools⁠，包含大量样板代码与未来用到的 ‎⁠add_duration_to_datetime⁠ 函数。
 2. 手动编写函数：暂不依赖现有辅助方法，专注工具函数本身。
 3. 代码示例：from datetime import datetime
    ```python
    def get_current_datetime(date_format="%Y-%m-%d %H:%M:%S"):
        if not date_format:
            raise ValueError("date_format cannot be empty")
        return datetime.now().strftime(date_format)
    ```
     • 默认返回形如 ‎⁠2025-08-11 23:42:35⁠ 的完整时间。  
     • 调用示例：‎⁠get_current_datetime("%H:%M")  # 仅返回小时:分钟⁠。 

4. 下一步：与 Claude 集成
    编写函数只是第一步。接下来需要：
     • 为函数撰写 JSON Schema，描述参数与返回值，让 Claude 理解如何调用。
     • 在聊天系统中注册工具：将函数与 Schema 一并传入，Claude 方可在对话中调用。
     • 后续重构：等熟悉工具流程后，再把现有的 ‎⁠addUserMessage⁠、‎⁠addAssistantMessage⁠ 等辅助方法改造，以便简化调用逻辑。 

5. 小结
    通过本节，你应掌握：
     • 工具函数的定位与作用
     • 编写、校验、抛错的最佳实践
     • 完整实现 ‎⁠get_current_datetime⁠ 的代码与测试方法
     • 将来如何把函数对接到 Claude 的工具体系

完成上述步骤后，Claude 就能随时为用户提供格式化的当前时间，为后续扩展（如“日期加减”、“提醒”）奠定模板。 

# 2025-08-10

# 第二十六课 Exercise on prompting

## 课程目标
- 提升一个已有的低质量 Prompt，利用之前学过的所有 Prompt Engineering 技巧。
- 数据集包含学术文章的段落（`content` 字段）。
- 任务：从段落中提取所有提到的**主题**，返回 JSON 字符串数组。

## 初始状态
- 现有 Prompt 表现差，平均分约 **2.8**。
- 输出不符合要求（非 JSON 数组或格式错误）。
- 目标：将平均分提升至 **7+**。

## 优化步骤

### 1. 清晰直接（Be Clear and Direct）
- 直接在第一行说明任务：
  ```
  Extract key topics mentioned from a passage of text from a scholarly article into a JSON array of strings.
  ```
- 明确要求返回 **JSON 字符串数组**。
- 立即评分提升至 **9.5**。

### 2. 添加结构（XML 标签）
- 为输入内容增加标签，使模型更易识别：
  ```xml
  <text>
  [文章段落内容]
  </text>
  ```
- 标签名与描述保持一致（如 `text` 对应第一行 prompt 中的“passage of text”）。

### 3. 具体化（Be Specific）
- 添加分步指令：
  ```
  Follow these steps:
  1. Closely examine the provided text.
  2. Identify each topic mentioned.
  3. Add each topic to a JSON array.
  4. Respond only with the JSON array. Do not provide any other text or commentary.
  ```

## 评分结果
- 改进后多次运行，分数稳定在 **9.5**。
- Prompt 在提取主题任务中表现稳定可靠。

## 总结
- **核心优化**：清晰直接说明任务 + XML 标签结构化输入 + 明确分步要求。
- 如果需要处理特殊格式或复杂任务，可增加 **One-shot / Multi-shot** 示例，但此例已足够达成目标。

# 2025-08-09

# 第二十四课：使用 XML 标签为 Prompt 提供结构

## 核心概念
- **目的**：通过在 Prompt 中使用 XML 标签，为插入的大量或多类型内容提供清晰的结构，帮助模型准确理解各部分的含义和归属。
- **适用场景**：
  - Prompt 中需要插入大量数据（如长篇销售记录、文档等）时。
  - 不同类型的内容（代码、文档、参数等）混合在一起，容易引起模型混淆。

## 方法与示例
1. **基本做法**
   - 自定义标签名，用尖括号包裹内容，例如：
     ```xml
     <sales_records>
       ...销售记录内容...
     </sales_records>
     ```
   - 标签名应尽量具体，如 `sales_records` 比 `data` 更有意义。
   
2. **为什么有用**
   - 让模型清楚不同内容块的含义，减少错误理解。
   - 特别适合调试类任务中区分“代码部分”和“文档部分”。

3. **调试场景示例**
   - 未加标签时：模型难以区分哪个是需要调试的代码，哪个是参考文档。
   - 加标签后：
     ```xml
     <my_code>
       ...需要调试的代码...
     </my_code>
     <docs>
       ...相关文档...
     </docs>
     ```

4. **在本课程任务中的应用**
   - 当前任务中，`height`、`weight`、`goal`、`restrictions` 等内容较短，理论上不易混淆。
   - 但仍可用标签增强语义清晰度，例如：
     ```xml
     <athlete_information>
       Height: xxx
       Weight: xxx
       Goal: xxx
       Restrictions: xxx
     </athlete_information>
     ```

## 实验效果
- 在示例中，添加 `<athlete_information>` 标签后，Prompt 评估分数从 **7.3** 提升到更高水平（提升幅度因模型和数据而异）。
- 大模型可能提升不明显，但在中低端模型上，结构化标签能显著减少理解偏差。

## 总结
- XML 标签是一种低成本、高通用性的 Prompt 优化方式。
- 标签应具备明确的语义，覆盖内容应成对包裹。
- 对长文本、混合内容尤为有效。
---
# 第二十五课：在 Prompt 中提供示例（One-shot & Multi-shot Prompting）

## 核心概念
- 在提示词中加入示例（Example），可以显著提升模型输出质量。
- **One-shot prompting**：提供一个示例。
- **Multi-shot prompting**：提供多个示例。
- 常用于：
  - 处理**边缘情况（corner cases）**。
  - 指定**复杂输出格式**。
  - 强化模型对特定任务的理解。

## 原理与好处
1. **通过实例引导模型**：明确告诉 Claude 给定输入时的理想输出。
2. **配合 XML 标签**：
   - 示例输入用 `<sample_input>` 包裹。
   - 示例输出用 `<ideal_output>` 包裹。
   - 结构清晰，模型易于识别内容类型。
3. **处理讽刺/隐喻等难判断情况**：
   - 例：推文 *"Yeah, sure, that was the best movie I've ever seen since Plan 9 from Outer Space"* 实际为负面情绪。
   - 在示例中明确告诉 Claude 这是 **negative**。

## 实践方法
1. 从已有 **高分或满分** 测试用例中选取示例。
2. 在 Prompt 中加入说明：
   ```
   Here is an example with a sample input and an ideal output:
   <sample_input>...</sample_input>
   <ideal_output>...</ideal_output>
   ```
3. 可选增强：添加**为什么这是理想输出**的解释（从评估报告中复制 reasoning 部分）。
4. 对复杂 JSON 等结构，直接给出一个格式正确的示例，让 Claude 学习结构。

## 示例场景
- 情感分析（含讽刺判断）
- 复杂 JSON 响应生成
- 技术文档生成（格式化要求）

## 本课实验结果
- 在原有 Prompt 基础上加入示例后，评估分数从 **7.3** 提升至 **7.96**。
- 提升幅度虽小，但对长期稳定高质量输出非常有效。

## 结论
- 在 Prompt 中提供示例是最有效的优化方法之一。
- **One-shot** 适用于任务简单或数据稀缺场景。
- **Multi-shot** 适用于任务复杂、需覆盖多种情况的场景。

# 2025-08-08

# 第二十一课 - Prompt Engineering 入门与项目初始化

## 🎯 课程目标  
- 从 **Prompt Evaluation** 转向 **Prompt Engineering**  
- 学习通过优化 prompt 来获得更高质量、更稳定的输出  
- 用同一项目贯穿整个模块：从初版 prompt → 多轮改进 → 提升评分  

---

## 📌 本课内容  
1. **模块结构**  
   - 第1-2节：编写初版 prompt（可能很差）  
   - 后续多节：逐步应用不同 Prompt Engineering 技术优化，并反复跑 eval  
   - 使用上一模块改进版的 **通用化 eval pipeline**（支持任意 prompt）  

2. **项目目标**  
   - 编写一个 prompt：根据运动员的 **身高、体重、目标、饮食限制** 生成 **一天的饮食计划**  
   - 输出应包含：
     - 每日总热量  
     - 宏量营养素分配  
     - 餐食及具体食物、份量、时间  

3. **新工具：PromptEvaluator 类**  
   - 集成数据集生成、模型评分等功能  
   - 支持并发（`max_concurrent_tasks`），可加快 eval 速度  
   - 并发过高会触发 API 限速，建议开始用 3 或以下（作者演示用 50）  

4. **数据集生成（generate_dataset 方法）**  
   - 输入：prompt 的用途描述 + 输入字段定义（height, weight, goal, restrictions）  
   - `num_test_cases` 建议 3（快速调试）  
   - 输出写入 `dataset.json`  

5. **初版 Prompt（run_prompt 方法）**  
   - 极简写法：  
     ```
     What should this person eat?
     Height: {height}
     Weight: {weight}
     Goal: {goal}
     Restrictions: {restrictions}
     ```
   - 将数据集中的每条 case 的输入插入模板  

6. **额外评分标准（extra_criteria 参数）**  
   - 在 model grader 中增加附加要求：  
     - 含每日热量总计  
     - 含宏量营养素比例  
     - 含具体食物、份量、时间  
   - 提高对输出的验证要求  

7. **首次评估结果**  
   - 分数极低（示例 2.32，因故意用弱模型）  
   - 生成 `output.html`，可浏览详细的测试报告（任务、评分、推理、解决方案等）  

---

## ✅ 本课结论  
- 已完成项目的 **目标设定 + 数据集生成 + 初版 prompt 编写 + 初次 eval**  
- 当前分数很差 → 后续视频将用不同的 Prompt Engineering 技术迭代优化 
---
# 第二十二课  Being clear and direct

## 核心目标
- 通过第一个 Prompt Engineering 技巧 **“清晰且直接”** 改进初版提示词，提高评估得分。
- 当前起始得分为 **2.32**，目标是逐步优化。

---

## 技巧说明：清晰且直接（Be Clear and Direct）
1. **重点位置**
   - **提示词第一行**是最重要的部分，决定了 Claude 对任务的理解。

2. **写法要点**
   - 使用简单直接的语言。
   - 用**动词**开头，明确告诉 Claude 要做什么。
   - 提供输出类型与内容范围的提示。

3. **示例**
   - `Write three paragraphs about how solar panels work.`  
     - 动词 `Write` 明确了任务是写作。
     - 指定了篇幅与主题。
   - `Identify three countries that use geothermal energy and for each include generation stats.`  
     - 动词 `Identify` 指明任务是找出信息，并补充了输出细节。

---

## 应用到当前项目
- 原 prompt 开头过于随意，没有直接传达任务。
- 改进后的开头示例：
  ```
  Generate a one-day meal plan for an athlete that meets their dietary restrictions.
  ```
  - 使用 `Generate` 作为动词开头，直接下达任务指令。
  - 简洁描述了输出目标与限制条件。

---

## 效果
- 运行新的 prompt 并重新评估。
- 得分从 **2.32** 提升到 **3.92**，有明显进步但仍需进一步优化。

---
# 第二十三课 Prompt Engineering - 保持具体性（Being Specific）

### 核心概念
在 Prompt 工程中，“具体性”意味着在提示中列出明确的指导方针（guidelines）或步骤（steps），以引导模型朝特定方向生成内容。  
具体性有助于减少输出的随机性，提高结果质量。

### 两种常见方式
1. **Guidelines（指导方针）**  
   - 列出输出应具备的属性、结构或限制条件。
   - 示例：
     - 控制字数（如：1000 字以内）。
     - 包含特定情节或角色。
     - 确保有明确的开头、中间和结尾。

2. **Steps（步骤指令）**  
   - 给模型一个分步思考和执行的路径。  
   - 示例：
     1. 先头脑风暴 3 个可能的创意。  
     2. 选择最有趣的一个。  
     3. 设计一个揭示该创意的场景。  
     4. 添加至少一个辅助角色。

### 实验结果
- 在“生成运动员一日饮食计划”的任务中：  
  - **添加 Guidelines** → 分数从 **3.92** 提升到 **7.86**。  
  - **添加 Steps** → 分数约为 **7.3**，稍低于纯 Guidelines，但依然显著提升。
- 结论：
  - 常规建议：优先使用 **Guidelines** 控制输出属性。
  - 对于复杂任务：结合 **Steps** 强制模型考虑更广的视角和细节。

### 使用建议
- **Guidelines** → 适用于几乎所有 Prompt，帮助确保输出格式、风格和关键要素。
- **Steps** → 在需要推理、分析、生成多阶段内容时尤为有效，例如：调查原因、策略制定、复杂创意任务。

# 2025-08-07

# 第 20 课：优化模型评分的提示词设计

本节课是一个实践练习，目标是**通过提供更多上下文信息来改进模型评分器（Model Grader）**，提升提示词（Prompt）的评估效果。

---

## 🎯 目标

为模型评分器提供“什么是好的解决方案”的标准，从而帮助它给出更有依据的评分与解释。

---

## 📌 实施步骤

### 步骤一：修改数据集生成提示词，加入“solution_criteria”字段

- 打开 `generate_dataset` 函数，找到之前用于生成测试用例的提示词。
- 修改提示词，让 Claude 除了返回 `task` 与 `format`，还返回 `solution_criteria`。
- 示例格式：

```json
{
  "task": "创建一个 JSON 配置",
  "format": "JSON",
  "solution_criteria": "应包含字段 X，格式应符合 AWS 配置规范，字段需正确嵌套"
}
```

- 重新运行该 cell，生成更新后的数据集文件 `dataset.json`，并确认每个测试用例都包含 `solution_criteria` 字段。

---

### 步骤二：将 solution_criteria 注入到评分提示词中

- 找到 `grade_by_model` 函数。
- 在模型评分提示词中，原本只包含：
  - 任务描述（Task）
  - AI 生成的解决方案（Output）

- 现在新增：
  - 「评估标准」说明段落，如：`“以下是评估该解决方案的标准：”`
  - 插入对应的 `solution_criteria` 内容。

---

## ✅ 效果验证

- 重新运行 `run_eval` 函数，执行提示词评估流程。
- 用 `json.dumps(results, indent=2)` 打印结果，查看输出内容是否更合理：
  - 是否包含任务、解决方案、solution_criteria、评分（score）和理由（reasoning）
  - 评分是否提升？
  - Reasoning 是否更具体、符合预期？

---

## 📌 小结

- 本课通过向模型评分器提供更多“好方案的参考标准”，使评分更加可靠、透明。
- 提升提示词效果的关键在于清晰传达预期，避免评分时模型“猜测标准”。


# 2025-08-06

打卡

# 2025-08-05

# 第 19 课：实现代码 Grader

本课讲解如何实现 Prompt 评估工作流中的代码评分器（code grader），用于验证 Claude 输出是否符合格式要求且具备语法正确性。

---

## 🎯 目标

- 只接受以下三种格式输出：Python、JSON、正则表达式（Regex）。
- 严格禁止任何解释、注释、多余内容。
- 验证输出是否具备语法正确性（使用解析器尝试 parse/compile）。

---

## ✅ 实现步骤

### Step 1：编写三个验证函数

创建三个函数：

- `validate_json(output)`：尝试使用 `json.loads` 解析。
- `validate_python(output)`：尝试用 Python 的 AST 解析代码。
- `validate_regex(output)`：尝试用 `re.compile()` 编译正则。

每个函数都返回分数：
- 成功解析则返回 `10`
- 否则返回 `0`

然后写一个 `grade_syntax(output, test_case)` 来根据 `test_case["format"]` 调用相应的验证函数。

### Step 2：更新数据集，加入 format 字段

数据集中的每条测试用例加入字段：
```json
{
  "task": "生成一个 JSON 配置文件",
  "format": "json"
}
```

支持三种格式值：`python`、`json`、`regex`

通过更新 `generate_dataset()` 函数中的 prompt，自动生成带 format 的数据。

### Step 3：更新 Draft Prompt 模板

原始 prompt 只要求“解决任务”，可能导致生成多余内容。

修改 Prompt：
- 明确要求只输出代码（Python/JSON/Regex）
- 不允许附加解释、注释、标头或其他无关信息
- 使用预填的 Assistant Message + stop sequence 强制 Claude 返回纯代码块

使用语言提示：```code 开头和 ``` 结尾，避免指定具体语言类型。

### Step 4：合并模型评分和语法评分

更新 `run_test_case()` 函数：

```python
model_grade = grade_by_model(test_case, output)
model_score = model_grade["score"]
syntax_score = grade_syntax(output, test_case)
final_score = (model_score + syntax_score) / 2
```

最后将 `score` 和 `reasoning` 放入返回结果。

---

## ✅ 测试结果

执行 `run_eval()`，输出结果如下：

```json
{
  "score": 8.166,
  "reasoning": "符合格式要求，代码结构清晰，逻辑正确"
}
```

---

## 📌 小结

- 代码 grader 是 prompt 评估中不可缺少的一环。
- 与模型 grader 搭配使用可以提供更客观全面的评分。
- 下一课将尝试优化 prompt，提升评估得分。

# 2025-08-04

# 第 18 课：实现 Model Grader

本课讲解如何在 Prompt Evaluation 工作流中实现基于模型的评分器（Model Grader），以实现自动化、客观的评估机制。

---

## 一、评分器的目标

评分器的作用是对 Claude 模型的输出进行评估，并给出一个客观的评分信号，通常是：
- 1 到 10 的整数（10表示高质量，1表示低质量）
- 或布尔值（True/False）

评分值并非必须是数字，但这种方式非常常见。

---

## 二、评分器类型

本课程介绍三类评分器：

### 1. 代码评分器（Code Grader）
- 由开发者编写 Python 函数，对输出执行检查。
- 可验证：
  - 输出长度是否合理
  - 是否包含或排除某些关键字
  - 语法是否有效（适用于 JSON、代码等）
  - 可读性、风格或复杂度等高级规则
- 返回一个评分值或布尔值。

### 2. 模型评分器（Model Grader）
- 使用另一个 Claude 模型调用，输入为 prompt 和 output。
- 可评估：
  - 是否符合指令
  - 回答是否完整
  - 逻辑是否正确
  - 响应的质量
- 灵活性强，返回 1~10 的评分值是常见方式。

### 3. 人工评分器（Human Grader）
- 人类评估模型的输出。
- 优点：灵活，支持主观、复杂的评估维度。
- 缺点：耗时、成本高、效率低。

---

## 三、设计评分标准（Evaluation Criteria）

为了统一评分逻辑，需要事先定义清晰的评估标准。本课选用以下三项：

1. **格式检查**：
   - 输出必须是 Python / JSON / 正则表达式之一。
   - 不包含解释、标题等无关内容。
   - 用代码评分器实现。

2. **语法校验**：
   - 保证输出语法有效。
   - 用代码评分器实现。

3. **任务完成度**：
   - 判断模型是否确实完成了用户任务。
   - 判断是否有重大错误或遗漏。
   - 使用模型评分器实现。

---

## 四、实现模型评分器

### 函数签名：
```python
def grade_by_model(test_case: dict, output: str) -> dict
```

### 实现步骤：

1. 构造评分用 Prompt（内容包括任务描述、输出、评分指令等）
2. 使用 `chat()` 接口发送 message 列表，附加 assistant 预填和 stop sequence
3. 获取并解析返回的 JSON 结构：
```json
{
  "score": 8,
  "reasoning": "输出内容合理且无逻辑错误"
}
```

4. 返回该 JSON 字典作为评分结果。

---

## 五、整合进测试流程

- 修改 `run_test_case()` 函数，调用 `grade_by_model()` 替代硬编码评分。
- 将 `score` 与 `reasoning` 合并进最终结果字典中。
- 可选：提取 `strengths` / `weaknesses` 字段做更深层分析。

---

## 六、汇总结果并计算平均分

- 修改 `run_eval()` 函数：
  - 遍历所有测试用例并评分
  - 汇总所有得分
  - 使用 Python 标准库 `statistics.mean()` 计算平均得分
  - 打印最终平均分作为 prompt 的客观表现指标

---

## 七、结果示例

运行后每个测试用例会输出：
- Claude 的响应文本
- 原始任务内容
- 模型评分（如 8、7、6）
- 评分理由

最终打印平均得分，例如：
```
Average Score: 7.33
```

---

## 小结

本课完成了 Prompt 评估工作流中最关键的一环——评分器的实现。通过引入模型评分器，我们能获取更接近真实的反馈，用于后续 prompt 的迭代优化。

# 2025-08-03

# 第十五课：构建 Prompt Evaluation 数据集（生成式）

本课我们继续构建一个自定义的 Prompt Evaluation 流程，目标是生成一个有效的数据集用于后续测试和评估 prompt 的性能。

---

## 🧠 目标

编写一个 prompt，让 Claude 输出以下三种格式之一的代码，无附加说明：

- Python 函数
- JSON 配置
- 正则表达式（raw regex）

---

## ✏️ 第一步：编写初始 prompt

```text
"请提供以下任务的解决方案：{task}"
```

该 prompt 的任务是根据用户输入的 task，生成一段符合要求的代码，不要包含解释说明或其他文本。

---

## 📦 第二步：生成数据集

我们需要一个输入数据集，其中每条数据是一个 JSON 对象，包含字段 `task`。这些任务将会被插入到 prompt 中测试 Claude 的输出。

数据集可以：

- 手动书写
- 使用 Claude 自动生成（推荐使用 Haiku 模型）

---

## 🔧 使用 Haiku 自动生成数据集

编写函数 `generate_dataset()`，使用 Claude + prefill + stop sequence 的方式获取标准 JSON 数组，包含多个任务对象：

```python
messages = []
add_user_message(messages, PROMPT)
add_assistant_message(messages, "```json")
text = chat(messages, stop_sequences=["```"])
return json.loads(text)
```

---

## 💾 保存数据集到文件

```python
with open("dataset.json", "w") as f:
    json.dump(dataset, f, indent=2)
```

保存后我们就有一个标准化的输入数据集，可供后续评估流程复用。

---

## ✅ 小结

- 本节课完成了 prompt 初稿编写
- 使用 Claude 生成了结构化任务数据集
- 并写入 dataset.json 文件，为后续 prompt 自动化评估打好基础

---

# 第十六课：执行 Prompt 测试并组织评估结果

本课继续构建自定义的 Prompt Evaluation 流程，重点是将生成好的数据集与 prompt 合并，批量发送请求到 Claude，并组织结果以供后续评分（grading）。

## 核心流程概览

我们实现了三个主要函数：

---

### 1. `run_prompt(test_case)`

- 输入：一个测试用例（即包含 `task` 字段的 JSON 对象）
- 功能：将 `task` 内容插入 prompt 模板中，构造 user message，调用 Claude 接口获得输出。
- 当前 prompt 示例：

```python
f"Please solve the following task:\n{test_case['task']}"
```

- 输出：Claude 返回的 raw 文本内容（尚未格式清洗）

---

### 2. `run_test_case(test_case)`

- 功能：负责执行一个完整的测试流程：
  - 调用 `run_prompt` 获取输出
  - （TODO）调用 grader 获取评分（当前 hardcode 为 10）
  - 返回包括输入、输出、评分的字典对象：

```python
{
  "test_case": ...,
  "output": ...,
  "score": ...
}
```

---

### 3. `run_eval(dataset)`

- 功能：对整个数据集执行测试流程
  - 读取 `dataset.json`
  - 遍历所有 test cases，调用 `run_test_case`
  - 返回所有结果组成的列表

---

## 执行测试

```python
with open("dataset.json") as f:
    dataset = json.load(f)

results = run_eval(dataset)
```

- 输出为多个测试结果组成的列表，每个元素为：

```json
{
  "test_case": {"task": "..."},
  "output": "...",
  "score": 10
}
```

---

## 当前进展总结

- ✅ 已完成 prompt 构造
- ✅ 数据集与 prompt 合并并调用 Claude
- ✅ 初步收集评估结果
- ❌ 评分逻辑未实现（下一课处理）

此阶段我们已实现完整的测试执行管道，为 grader 评分打下了基础。

# 2025-08-02

# 第十五课：构建 Prompt Evaluation 数据集（生成式）

本课我们继续构建一个自定义的 Prompt Evaluation 流程，目标是生成一个有效的数据集用于后续测试和评估 prompt 的性能。

---

## 🧠 目标

编写一个 prompt，让 Claude 输出以下三种格式之一的代码，无附加说明：

- Python 函数
- JSON 配置
- 正则表达式（raw regex）

---

## ✏️ 第一步：编写初始 prompt

```text
"请提供以下任务的解决方案：{task}"
```

该 prompt 的任务是根据用户输入的 task，生成一段符合要求的代码，不要包含解释说明或其他文本。

---

## 📦 第二步：生成数据集

我们需要一个输入数据集，其中每条数据是一个 JSON 对象，包含字段 `task`。这些任务将会被插入到 prompt 中测试 Claude 的输出。

数据集可以：

- 手动书写
- 使用 Claude 自动生成（推荐使用 Haiku 模型）

---

## 🔧 使用 Haiku 自动生成数据集

编写函数 `generate_dataset()`，使用 Claude + prefill + stop sequence 的方式获取标准 JSON 数组，包含多个任务对象：

```python
messages = []
add_user_message(messages, PROMPT)
add_assistant_message(messages, "```json")
text = chat(messages, stop_sequences=["```"])
return json.loads(text)
```

---

## 💾 保存数据集到文件

```python
with open("dataset.json", "w") as f:
    json.dump(dataset, f, indent=2)
```

保存后我们就有一个标准化的输入数据集，可供后续评估流程复用。

---

## ✅ 小结

- 本节课完成了 prompt 初稿编写
- 使用 Claude 生成了结构化任务数据集
- 并写入 dataset.json 文件，为后续 prompt 自动化评估打好基础

# 2025-08-01

下面是Prompt evaluation章节的第一课

# 第十四课：Prompt Evaluation 简介

## 本节目标

本节课主要介绍两个核心概念：

- **Prompt Engineering（提示词工程）**
- **Prompt Evaluation（提示词评估）**

我们将重点学习如何评估提示词的效果，以及如何在工程实践中选择更高质量的提示。

---

## Prompt Engineering 与 Prompt Evaluation 的区别

| 概念 | 作用 |
|------|------|
| **Prompt Engineering** | 编写或修改提示词的技术集合，目的是让 Claude 更准确地理解我们的意图并生成期望的响应。 |
| **Prompt Evaluation** | 使用自动化手段对提示词进行测试，产出客观指标（如评分、准确率等）来衡量提示词的效果。 |

---

## 为什么需要 Prompt Evaluation？

大多数人写完 prompt 后通常走这三种路径之一：

1. 写完 prompt，简单测试几次，感觉还可以就直接上线。
2. 写完 prompt，使用几个自定义输入测试后，微调以覆盖一些边缘情况。
3. 写完 prompt 后，将其送入 **评估流程（evaluation pipeline）**，通过自动化测试获得一个客观分数，然后再进行迭代优化。

:::info 建议
前两种做法看似省事，其实是**陷阱**，大多数工程师都中招过。如果提示词将用于生产环境，强烈建议走第 3 条路线。
:::

---

## 小结

- Prompt Evaluation 是 prompt 开发流程中的重要一环。
- 使用自动化测试可以帮助我们更客观地评估提示词质量。

# 第十五课：Prompt Evaluation 流程概览

本课介绍了一个典型的 Prompt Evaluation（提示词评估）工作流程的完整流程，并强调了两个核心原则：

## 关键前提

1. **没有固定标准流程**：业界并无统一标准，不同团队可根据实际需要自定义流程。
2. **不需依赖复杂工具**：评估可以从零开始用简单代码实现，再逐步演进为成熟方案。

---

## Prompt Evaluation 工作流程步骤

### Step 1：编写初始 Prompt

编写一个待评估的基础提示词。例如：

```
请回答用户的问题：{{user_question}}
```

### Step 2：准备评估数据集

构建输入样本（如用户问题），用于批量测试 prompt 的效果。样本数量可以从少量开始，逐步扩展至成百上千条。样本可以手工构造，也可以借助 Claude 生成。

示例：
```python
dataset = [
    "2+2 等于几？",
    "燕麦粥怎么做？",
    "地球到月球的距离是多少？"
]
```

### Step 3：填充 Prompt + 获取模型输出

将每个样本问题填入 prompt 中，生成完整请求文本，并发送给 Claude 获取回答。

```text
请回答用户的问题：2+2 等于几？
```

对每个样本重复执行，收集所有回答。

### Step 4：对输出打分（评分器）

将每条问题与对应回答传入评分器（grader），输出一个 1~10 的分数，衡量回答质量。

- 10 分：几乎完美，无需改进。
- 4 分：需要明显优化。

评分器实现方式多种多样，后续课程会详细讲解。

### Step 5：计算平均得分

对所有评分求平均值，得到该 prompt 的“客观质量指标”。

例如：
```text
评分结果 = [10, 7, 6] → 平均分 = 7.66
```

### Step 6：迭代优化 Prompt

基于评分结果调整 prompt，例如添加更多引导信息，引导模型产出更清晰或更详细的回答。

示例改进：
```text
请详细回答用户的问题。
```

再重复整个流程，比较 V1 与 V2 提示词得分，以确定哪一个效果更好。

---

## 总结

Prompt Evaluation 的关键流程为：

1. 写 Prompt
2. 准备数据集
3. 运行生成
4. 自动打分
5. 求平均分
6. 迭代优化

# 2025-07-30

# 🧠 第十一课：预填消息 & 停止序列（Controlling Claude's Output）

---

## 🎯 本课目标

- 学会使用 **Assistant Message 预填（Pre-filling）** 引导 Claude 输出倾向
- 学会使用 **Stop Sequences** 控制 Claude 生成中止位置

---

## 1️⃣ Assistant Message 预填（Pre-filling）

### 🤔 背景场景

提问：
```
"Is tea or coffee better at breakfast?"
```

Claude 默认返回中立回答（例如：两者皆有优点）。

### 🎯 目标：引导 Claude 立场

通过“预填助手消息”模拟 Claude 的“自我暗示”行为：

```python
add_user_message(messages, "Is tea or coffee better at breakfast?")
add_assistant_message(messages, "Coffee is better because")
response = chat(messages)
```

Claude 会自动将 `"Coffee is better because"` 当作它自己“刚刚说出”的部分，然后续写下去，如：

```
Coffee is better because it contains more caffeine and helps kickstart your day.
```

### 💡 可替换不同引导方向

```python
add_assistant_message(messages, "Tea is better because")
add_assistant_message(messages, "Neither is very good because")
```

---

## 2️⃣ Stop Sequences（停止生成的控制符）

### 🛑 场景说明

你希望 Claude 的输出在遇到特定字符串时立即中止。

例如：

```python
add_user_message(messages, "Count from 1 to 10")
chat(messages, stop_sequences=["5"])
```

Claude 一旦生成 `"5"`，将立即终止输出。**注意：包含 stop 序列的 token 本身不会出现在返回结果中。**

### 🧪 示例优化

如果希望停止在 `" , 5"`，避免保留前缀标点，可设置：

```python
stop_sequences=[", 5"]
```

---

## 🔧 技术实现小结

| 技术 | 使用方式 | 应用场景 |
|------|----------|----------|
| Assistant 预填 | `add_assistant_message` | 引导生成方向、保持风格一致 |
| Stop Sequences | `chat(..., stop_sequences=["标记"])` | 限定生成范围、用于控制输出截断（如脱敏、格式化） |

---

## 🧰 chat() 函数增强建议

在 `chat()` 函数签名中加入：

```python
def chat(messages, system=None, temperature=1.0, stop_sequences=[]):
    ...
    if stop_sequences:
        params["stop_sequences"] = stop_sequences
```

即可支持完整的停用词功能。

---

📌 **这一课展示了仅靠 prompt 无法实现的高级控制技术，适用于更复杂或更高要求的生成场景。**
# 第十二课：结合使用 Assistant Message 预填充 与 Stop Sequence

在使用 Claude 生成结构化数据（如 JSON、Python 代码或列表）时，Claude 往往会在返回内容中添加额外的说明、头部或注释信息。这在某些场景下会破坏用户体验，特别是当我们只想获取**纯粹的结构化输出**时。

## 解决方案：预填充 + Stop Sequence

为了解决这个问题，我们可以结合使用：

- **Assistant Message 预填充**：人为指定 Claude 回应的起始片段。
- **Stop Sequences**：指定 Claude 遇到特定字符串时立刻停止输出。

## 示例：生成 AWS EventBridge JSON 规则

设想我们构建了一个 Web 应用，用户输入需求后点击“生成”，希望得到一段 EventBridge 规则（JSON 格式）。我们希望输出 **只包含 JSON**，不包含注释或 Markdown 格式。

### 不加控制的输出问题

Claude 默认可能返回如下格式：

```
```json
{
  "source": ["aws.ec2"],
  ...
}
``` 
```

这种包含 Markdown 代码块标记（```）的输出不利于程序提取纯粹内容。

### 解决方案代码实现

```python
messages = []
add_user_message(messages, "Generate a very short EventBridge rule as JSON.")
add_assistant_message(messages, "```json")  # 预填充开头
response = chat(messages, stop_sequences=["```"])  # 设置停止标记
```

Claude 会认为自己已经开始写 JSON，因此不会添加解释性语言。遇到结尾的 ``` 时立刻停止输出。

我们可以进一步使用 `json.loads()` 和 `strip()` 对输出做清理：

```python
import json
cleaned = json.loads(response.strip())
```

## 原理解析图解（简要）

- Claude 接收到用户消息，准备写规则。
- 遇到 assistant 预填充 `"```json"`，认为自己已经开始写 JSON。
- 按照 JSON 写入内容。
- 一旦生成结尾的 `"```"`，触发 stop sequence，立即终止输出。

## 应用场景

此技术非常适用于生成以下结构化数据时：

- JSON / XML / YAML
- Python、Shell 等代码段
- Markdown 列表、表格
- SQL 语句、配置片段等

目标是**获取纯净内容，无头尾注释、无解释性描述**。

---

> ✅ 建议在所有结构化输出需求中采用 Assistant 预填充 + Stop Sequences 组合。
# 第十三课：练习 Stop Sequence 与 Assistant Message 预填

本课是对上一课内容的练习，目标是熟练掌握如何使用 Stop Sequence 和 Assistant 预填（Prefill）控制 Claude 的输出格式，特别是输出结构化内容（如命令列表）时避免冗余说明或多余格式。

---

## 🎯 目标

- 通过 Claude 生成三个 AWS CLI 示例命令
- 输出中不能包含说明文字、注释或格式化标签（如 Markdown 标题）

---

## 🧪 初始尝试（错误示范）

使用如下 prompt：

```
generate three different sample AWS CLI commands
```

Claude 会返回：

- 顶部有说明性文字
- 每个命令前有编号或注释
- 被 Markdown 的 ``` 包裹

这些都是我们不想要的。

---

## ✅ 正确做法

### 1. 使用 Assistant Message 预填控制格式

预填内容：

```
```bash
```

含义是告诉 Claude：
> “你已经开始写 Markdown 代码块，并使用 bash 语法高亮”

Claude 会紧接着开始生成命令内容。

### 2. 使用 Stop Sequence 控制结尾

设置 stop sequence 为：

```
```（三个反引号）
```

这样 Claude 会在输出到 ``` 时立刻中断，避免输出结束语或说明。

---

## 💡 最佳写法

预填内容如下：

```
Here are all three commands in a single block without any comments:
```bash
```

注意：

- 明确告诉 Claude 我们要“单一代码块、无注释”
- 换行后再接代码格式，有助于避免开头插入说明

配合 stop sequence ``` ，Claude 将仅生成命令本体。

---

## 🧾 示例输出（符合预期）

```bash
aws s3 ls
aws ec2 describe-instances
aws lambda list-functions
```

无标题、无编号、无注释、无多余内容。符合结构化输出场景需求。

---

## ✅ 小结

- 使用 assistant message 预填明确 Claude 的起始输出格式
- 使用 stop sequence 控制终止时机，避免结尾废话
- 针对结构化输出（代码块、JSON、列表等）这是通用做法

# 2025-07-29

# 🧠 第九课：理解 Claude 的 Temperature 参数

## 🔄 Claude 的文本生成流程回顾

Claude 生成文本的典型流程：

1. **接收输入文本**
   - 例子："What do you think"

2. **Tokenize**（分词）
   - 将句子拆成更小的 token（语义单位）

3. **预测下一个 token 的概率分布**
   - 可能是："about", "would", "of" 等
   - 每个 token 有一个概率值

4. **采样（Sampling）**
   - 根据概率分布选择一个 token，作为下一个词

5. **重复步骤 2~4** 直到生成完整回答

---

## 🎛 Temperature 控制：影响采样的“随机性”

**参数范围：** `0.0 ~ 1.0`  
**默认值：** `1.0`

### 作用：

- 控制模型在生成文本时的“创造力”
- **低温度（如 0.0）**：
  - 更确定性（总是选择概率最高的 token）
  - 输出稳定、一致性强
- **高温度（如 1.0）**：
  - 更多样化、创造力强
  - 输出更不可预测

---

## 📊 图示理解

| Temperature | 效果 |
|-------------|------|
| 0.0         | 完全确定性，始终输出最高概率词 |
| 0.3         | 轻微多样性 |
| 0.7         | 平衡稳定性与创造性 |
| 1.0         | 高创造性、更发散 |

---

## 🧪 示例：一行电影点子生成

**Prompt：** "Give me a one-sentence movie idea"

### Temperature = 0.0

- "A time-traveling archaeologist discovers..."
- "A time-traveling historian battles..."

重复出现 “time-traveling” 的内容。

### Temperature = 1.0

- "An AI comedian trapped in a joke simulation"
- "A city made entirely of forgotten dreams"

更富想象力、多样化。

---

## 🔧 代码修改支持 temperature 参数

### 修改 chat 函数：

```python
def chat(messages, system=None, temperature=1.0):
    params = {
        "model": model,
        "max_tokens": 1000,
        "messages": messages,
        "temperature": temperature
    }
    if system is not None:
        params["system"] = system
    response = client.messages.create(**params)
    return response.content[0].text
```

---

## ✅ 总结

| 任务类型         | 推荐 Temperature |
|------------------|------------------|
| 数据抽取、问答   | 0.0 ~ 0.3        |
| 内容生成（写作） | 0.7 ~ 1.0        |
| 营销、创意内容   | 0.9 ~ 1.0        |
| 代码生成         | 0.2 ~ 0.6        |

合理设置 temperature 能更好地控制 Claude 的响应风格和稳定性。

---

# 🔄 第十课：使用 Claude Streaming 实现实时响应

## 🎯 问题背景

Claude 传统接口存在响应延迟：

```text
用户提交 ➜ Claude 生成完整内容 ➜ 返回 ➜ 页面才展示
```

这在用户输入和输出都较长时，会造成**10~30 秒等待**，体验差。

---

## ⚡ 解决方案：使用 Claude 的 Streaming API

### 流式交互流程

```text
用户输入 ➜ Claude 接收请求 ➜ 实时返回 token 块 ➜ 页面逐步显示
```

---

## 🧩 Streaming 响应结构

Claude 的响应是一系列事件流：

1. `message_start`
2. `content_block_start`
3. `content_block_delta` ✅（含真实内容）
4. `content_block_stop`
5. `message_delta`
6. `message_stop`

我们主要提取 `content_block_delta` 中的文本片段。

---

## 🧪 示例 1：打印原始事件

```python
stream = client.messages.create(
    model=model,
    max_tokens=1000,
    messages=messages,
    stream=True
)

for event in stream:
    print(event)
```

---

## 🧪 示例 2：提取纯文本

Anthropic SDK 提供 `.text_stream` 简化操作：

```python
with client.messages.stream(
    model=model,
    max_tokens=1000,
    messages=messages
) as stream:
    for text in stream.text_stream:
        print(text, end="")
```

---

## 🧪 示例 3：收集完整响应

可用于写数据库：

```python
with client.messages.stream(
    model=model,
    max_tokens=1000,
    messages=messages
) as stream:
    for _ in stream.text_stream:
        pass  # 实时显示

    full_message = stream.get_final_message()
    # 存入数据库或进一步处理
```

---

## 📌 总结

| 功能               | 方法                             |
|--------------------|----------------------------------|
| 启用流式响应       | `stream=True`                    |
| 提取文本块         | `for text in stream.text_stream` |
| 收集完整消息       | `stream.get_final_message()`     |

使用 Streaming 可大幅提升响应体验，是构建实时聊天体验的关键。

# 2025-07-27

# 🧑‍🏫 第七课：使用 System Prompt 自定义 Claude 回答风格

---

## 🎯 课程目标

- 学会控制 Claude 的语气、风格和行为
- 理解并使用 `system prompt` 指导 Claude
- 支持不同场景下的角色设定（如教学辅助、客服、助手等）
- 重构 `chat()` 函数支持可选系统提示参数

---

## 🤔 使用场景：数学家教机器人

目标场景设定：

- Claude 是一位耐心的数学导师
- 学生提问如：`5x + 2 = 3 怎么解？`
- Claude 不能直接给出答案，而是要：
  - 提供提示（hints）
  - 展示相似问题的解法
  - 分步引导推理过程
- ❌ 不允许：
  - 直接给出答案
  - 建议用计算器应付

---

## ✨ System Prompt 是什么？

- 一段**字符串描述**
- 告诉 Claude 应该**扮演什么角色**、**如何回答**
- 作为参数 `system` 传入 Claude 的 `create()` 方法中

### 示例：

```python
system = '''
You are a patient and encouraging math tutor.
Never give direct answers.
Instead, guide the student step by step through reasoning.
'''
```

---

## ⚙️ Claude 的对比实验

### 🚫 无 System Prompt（默认行为）：

```python
messages = []
add_user_message(messages, "Solve 5x + 3 = 2 for x.")
response = chat(messages)
print(response)
```

Claude 会直接输出详细步骤和答案（对学习不利）。

---

### ✅ 使用 System Prompt（引导式）：

```python
response = chat(messages, system=system)
```

Claude 会改为**提示式引导**，如：

> "To isolate x, what should we do first with the constants in the equation?"

---

## 🔁 改进：将 System Prompt 变成可选参数

### 旧版本 `chat()` 缺点：

- `system` 是硬编码的，无法灵活复用
- 所有调用都固定角色

---

### ✅ 新版本：传参可选，动态组装参数

```python
def chat(messages, system=None):
    params = {
        "model": model,
        "max_tokens": 1000,
        "messages": messages
    }

    if system is not None:
        params["system"] = system

    response = client.messages.create(**params)
    return response.content[0].text
```

好处：

- 没有系统提示也能正常调用
- 想指定风格时只需加一行传参
- 更通用、更可复用

---

## 🧠 总结

| 能力 | 内容 |
|------|------|
| ✅ 控制语气风格 | 使用 `system prompt` 设定角色 |
| ✅ 提高交互性 | Claude 不再死板回答，而是引导思考 |
| ✅ 提升代码可维护性 | 动态传参，灵活控制 system 提示 |
| ✅ 实战能力提升 | 为后续构建教学助手、客服、个性化机器人打基础 |

---

下一节很可能深入**Prompt 结构设计技巧**或探讨更多系统角色模板的构造方式。

# 🧪 第八课：控制 Claude 代码输出风格（System Prompt 实战练习）

---

## 🎯 本节目标

- 学会通过 `system prompt` 控制生成代码的风格
- 尝试减少 Claude 回答中的冗余内容（注释/解释）
- 体验使用 Claude 构建“极简主义”风格的代码助手

---

## 📌 实战任务描述

> 编写一个 Python 函数：检查字符串中是否存在重复字符。

默认调用 Claude 时，它可能会：

- 输出完整函数；
- 附带解释说明；
- 添加大量注释；
- 输出格式较“啰嗦”。

---

## ✨ 提升目标：生成更简洁的实现

### 🧠 技巧：使用 System Prompt 引导 Claude

你可以通过以下 prompt 来设定 Claude 的角色：

```python
system = "You are a Python engineer who writes very concise code."
```

然后调用 `chat()` 并传入该 prompt：

```python
response = chat(messages, system=system)
```

---

## 🧾 示例结构

### 用户提问：

```python
add_user_message(messages, "Write a Python function that checks if a string has duplicate characters.")
```

### Claude 返回（简洁风格）：

```python
def has_duplicates(s):
    return len(set(s)) != len(s)
```

无需注释，无需解释，**只有核心逻辑**。

---

## ✅ 小结

| 重点 | 说明 |
|------|------|
| 🎯 控制输出风格 | 使用 system prompt 精准定义风格 |
| ✂️ 精简 Claude 输出 | 减少不必要注释/解释 |
| 🧰 Prompt 模板实用化 | 可复用于各种代码场景（工程师助手） |

---

## 💡 系统提示建议模板

你可以尝试以下风格指令：

- `You are a senior software engineer who prefers minimalism.`
- `Reply with only Python code and no explanation.`
- `Do not include any comments or print statements.`

这些将帮助你获得更加“生产环境友好”的输出。

---

📌 本节是 Claude 代码生成控制能力的初步实战，后续课程可能深入 Prompt 模板设计、行为控制与安全限制等内容。


# 2025-07-26

# 🧠 第五课：构建 Claude 的上下文记忆机制

## 📌 课程目标：

- 理解 Claude API 的无状态性。
- 学习如何手动维护消息上下文。
- 编写可复用的助手函数，实现多轮对话能力。


## ❗ Claude 是无状态的

Claude（以及 Anthropic API）不存储对话历史，不保存用户发送的消息和 Claude 返回的回复。

因此，若想实现多轮上下文对话，需：

1. 在本地维护完整的消息列表（包括用户和助理）。
2. 每次请求都提交完整的消息列表。


## 🔁 示例说明：错误的对话续写方式

### ❌ 错误方式（无上下文）：

```python
message = client.messages.create(
    model=model,
    max_tokens=1000,
    messages=[{
        “role”: “user”,
        “content”: “Write another sentence.”
    }]
)
```

结果：Claude 无法关联前文，返回与“量子计算”无关的内容。


## ✅ 正确方式：维护完整消息历史

### 示例结构（伪代码）：

```python
messages = [
    {“role”: “user”, “content”: “What is quantum computing?”},
    {“role”: “assistant”, “content”: “Quantum computing is …”},
    {“role”: “user”, “content”: “Write another sentence.”}
]
```

每次调用 Claude API，都传入上述完整 `messages`。


## 🛠️ 创建 3 个复用助手函数

### 1. 添加 User 消息：

```python
def add_user_message(messages, text):
1. 添加用户消息：

```python
def add_user_message(messages, text):
    user_msg = {“role”: “user”, “content”: text}
    messages.append(user_msg)
```

2. 添加Assistant消息：

```python
def add_assistant_message(messages, text):
    assistant_msg = {“role”: “assistant”, “content”: text}
    messages.append(assistant_msg)
```

3. 执行Claude对话请求：

```python
def chat(messages):
    response = client.messages.create(
        model=model,
        max_tokens=1000,
        messages=messages
    )
    return response.content[0].text
```

## 💬 多轮对话示例

```python
# 初始化上下文
messages = []

# 第一句提问
add_user_message(messages, “Define quantum computing in one sentence.”)
answer = chat(messages)
print(answer)

# 保存 Claude 的回复
add_assistant_message(messages, answer)

# 第二轮提问
add_user_message(messages, “Write another sentence.”)
answer = chat(messages)
print(answer)
```

Claude会输出一条延续前一条回答的句子，确保上下文连贯。

## 🧠 小结

- Claude是无状态模型，每轮请求都需提交完整上下文；
- 实现了3个可复用的辅助函数：add_user_message、add_assistant_message和chat；
- 支持构建连续自然的多轮交互体验；
- 后续课程将继续基于此框架深入构建高级Agent。
- 后续代码将提交至[github](https://github.com/joyc/anthropic-claude-course)

# 🤖 第六课：构建循环对话的 Claude 聊天机器人（Jupyter 版）

## 🎯 课程目标：

- 在 Notebook 中运行一个简单的 Claude 聊天机器人。
- 实现输入 → 生成 → 输出 → 再输入的循环机制。
- 继续复用之前课程实现的 `chat()`、`add_user_message()` 和 `add_assistant_message()`。

## 🔄 聊天循环结构说明：

程序执行流程如下：

1. 使用 `input()` 获取用户输入。
2. 将用户输入加入消息列表（`messages`）。
3. 使用 `chat()` 调用 Claude 并获取回复。
4. 将回复加入消息列表。
5. 打印回复并回到第 1 步。
6. 重复执行，直到手动中断（如点击“停止”按钮或按 `Esc`）。

## 🧱 示例模板结构：

```python
# 初始化对话历史
messages = []

# 无限循环
while True:
    # 获取用户输入
    user_input = input(“👤 You: “)

    # 加入用户消息
    add_user_message(messages, user_input)

    # Claude 回复
    response = chat(messages)

    # 加入 Claude 回复
    add_assistant_message(messages, response)

    # 打印回复
    print(“\n🤖 Claude:\n—\n” + response + “\n—\n”)
```

## 💡 示例对话演示：

```
👤 You: What’s 1 + 1?
🤖 Claude:
—
1 + 1 equals 2.
—

👤 You: Add two to that answer.
🤖 Claude:
—
Adding 2 to the previous answer (2) gives 4.
—
```

🧠 Claude 能保留上下文的关键就在于你始终传入完整的 `messages` 列表！

—

## ✅ 本节收获：

- 掌握 `while True + input()` 搭建人机对话循环。
- 巩固了 `messages` 上下文在多轮对话中的作用。
- 拥有了一个最小可用的 Claude 聊天机器人原型。

你现在可以在本地构建、测试各种 prompt 和多轮逻辑了！

下一课可能进入系统提示设计或高级上下文管理策略。



# 2025-07-25

# 第一节：课程导论（Course Introduction）

## 👨‍🏫 讲师信息
- 姓名：Stephen Greider
- 公司：Anthropic，技术团队成员

---
## 🗂️ 本课程涵盖内容
1. Claude 模型介绍
2. Anthropic API 使用
3. Prompt 工程与评估
4. 工具调用（Tool Use）
5. RAG（检索增强生成）
6. MCP（Model Context Protocol）扩展服务
7. 官方 Agents：Cloud Code & Computer Use
8. 多步骤任务与 Agent 工作流

---

## 🛠️ 课程学习要求
- 基础 Python 知识
- 可运行 Notebook 的 Python 环境
- Anthropic API Key（课程中提供申请方法）

## 学习建议
- 💻 动手编写代码：跟随视频边学边做
- ⏩ 倍速播放：加快进度节省时间，方便回顾重点
- 🧪 实际应用：尝试修改、扩展 Notebook 示例
- 🧠 遇阻就问 Claude：任何问题都可以让 Claude 协助解决

---
# 第二节：Claude 三大模型家族与选型指南

## 📌 核心观点
Claude 有 **三个模型家族**，它们功能相似，但在智能、速度和成本上各有优化重点。

---

## 📊 模型对比表

| 模型     | 智能 | 速度 | 成本 | 特点说明 |
|----------|------|------|------|----------|
| **Opus** | ⭐⭐⭐⭐ | ⭐⭐   | 💰💰💰 | 最智能，适合复杂、多步骤任务 |
| **Sonnet** | ⭐⭐⭐  | ⭐⭐⭐ | 💰💰  | 平衡型，适合大多数应用场景 |
| **Haiku** | ⭐⭐   | ⭐⭐⭐⭐ | 💰    | 最快，适合实时高频交互 |

---

## 🧠 选型指南

- 🧩 **复杂推理、优先质量** → `选择 Opus`
- ⚡ **实时响应、优先速度** → `选择 Haiku`
- ⚖️ **通用场景、均衡考虑** → `选择 Sonnet`

---

## 🔁 实战建议：组合使用模型

- `Haiku`：用户界面实时交互  
- `Sonnet`：主业务逻辑处理  
- `Opus`：需要深度智能的复杂任务

---

## 🧪 本课程使用模型
- 默认使用 **Sonnet** 模型
- 原因：智能、速度、成本三者兼顾，最适合教学和开发实践

---
# 🧠 第三课：通过 API 使用 Claude 并生成文本

---

## 🎯 课程目标

本节课将带你了解如何通过 Anthropic API 调用 Claude，并生成文本。  
我们将：
- 模拟一个基础聊天应用的后端流程；
- 讲解完整的请求生命周期；
- 探索 Claude 内部的文本生成机制。

---

## 🧱 Claude API 请求生命周期：5 个步骤

### ✅ **Step 1：前端用户发起请求**

- 用户在 Web App 中输入消息，点击“发送”；
- 消息被发送至你自己实现的后端服务器。

⚠️ **安全提示**：  
> 不要从前端（Web/Mobile）直接调用 Claude API。API 请求必须包含 Secret Key，应仅在后端调用。

---

### ✅ **Step 2：后端向 Claude API 发起请求**

- 后端接收到用户请求后，使用 Anthropic 提供的 **SDK 或 HTTP 请求**调用 API。
- 官方 SDK 支持语言包括：`Python`、`TypeScript`、`JavaScript`、`Go`、`Ruby`。

📦 必填字段：

```json
{
  "api_key": "你的密钥",
  "model": "模型名称",
  "messages": [...],
  "max_tokens": 300
}
```

---

### ✅ **Step 3：Claude 内部文本生成流程（语言模型机制）**

Claude 使用大型语言模型进行生成，过程可拆为 **四个阶段**：

1. **Tokenization（分词）**
   - 输入文本被拆解为 token（单词、部分词、符号、空格等）。
   - 示例：`"quantum"` → `["quantum"]`

2. **Embedding（向量化）**
   - 每个 token 被转换成一个 embedding（数字向量），表示其所有可能含义。

3. **Contextualization（上下文精化）**
   - Claude 根据前后 token 对每个 embedding 进行调整；
   - 使每个 token 的含义在当前上下文中唯一明确。

4. **Generation（生成）**
   - 模型预测下一个 token 的概率分布；
   - 结合概率和随机性选择下一个词；
   - 将其添加进已有的 token 列表，重复该过程直到生成终止。

---

### ✅ **Step 4：生成停止的判断机制**

Claude 每生成一个 token 都会判断是否应停止：
- 是否超过 `max_tokens` 限制；
- 是否生成了特殊的 `end_of_sequence` 停止符。

---

### ✅ **Step 5：响应返回与前端展示**

- API 返回结果至后端服务器，包含：
```json
{
  "completion": "生成的文本",
  "usage": {
    "input_tokens": 123,
    "output_tokens": 234
  },
  "stop_reason": "end_of_sequence"
}
```

- 后端将 `completion` 结果返回给前端展示在聊天窗口中。

---

## 📚 小结

- 本节目的：帮助你理解 Claude 的调用流程；
- 熟悉一些常见术语与生成机制；
- 不必死记细节，后续课程将通过实战进一步巩固。

---
# 💻 第四课：用代码调用 Claude API（入门实践）

---

## 🎯 本节目标

通过实际代码演示，完成一次最基本的 Claude API 请求。  
你将学会：

- 安装 SDK 和配置环境变量
- 创建 API 客户端
- 理解 `messages` 的结构和作用
- 发送一次完整的请求并解析 Claude 的回答

---

## 🧰 步骤 1：安装依赖

打开 Jupyter Notebook，并执行如下命令：

```python
%pip install anthropic python-dotenv
```

⚠️ VS Code 中 `%` 可能会提示语法错误，可以忽略。

---

## 🔐 步骤 2：配置 API Key

1. 在 Notebook 同级目录下创建 `.env` 文件；
2. 填入你的 API Key，格式如下：

```
ANTHROPIC_API_KEY="your_api_key_here"
```

✅ 建议把 `.env` 加入 `.gitignore`，防止密钥被上传至公共仓库。

3. 在 Notebook 中加载该环境变量：

```python
from dotenv import load_dotenv
import os

load_dotenv()
api_key = os.getenv("ANTHROPIC_API_KEY")
```

---

## 🤖 步骤 3：创建 API 客户端

```python
from anthropic import Anthropic

client = Anthropic(api_key=api_key)
model = "claude-3-sonnet-20240229"
```

---

## 📤 步骤 4：发送一次 API 请求

Claude SDK 的 `client.messages.create()` 方法接收三个关键参数：

- `model`：模型名称（如 Sonnet）
- `max_tokens`：最大生成长度限制
- `messages`：对话历史（数组格式）

### ⚠️ 关于 `max_tokens`

- 它是上限，而非目标长度；
- Claude 不会“故意”生成刚好这么长，只是遇到限制会自动终止。

---

## 📩 请求结构举例

```python
message = client.messages.create(
    model=model,
    max_tokens=1000,
    messages=[
        {
            "role": "user",
            "content": "What is quantum computing? Answer in one sentence."
        }
    ]
)
```

---

## 📝 响应处理

Claude 的返回结果结构较深，文本内容嵌套在：

```python
print(message.content[0].text)
```

只打印 Claude 返回的最终答案，忽略其他属性（如 token 使用情况、stop reason 等）。

---

## ✅ 本节总结

- 成功通过代码发送 Claude 请求；
- 熟悉 `messages` 结构：由 `user` 或 `assistant` 消息构成；
- 为后续构建对话式系统打下基础。


# 2025-07-24

<!-- Content_END -->
