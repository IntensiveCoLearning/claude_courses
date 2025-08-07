---
timezone: UTC+8
---

# don

**GitHub ID:** wangyidong3

**Telegram:** @don

## Self-introduction

想学习,努力跟上

## Notes

<!-- Content_START -->
# 2025-08-07

Structure with XML tags

Using XML Tags for Clarity

XML tags act as delimiters that help Claude understand the structure of your prompt. You can create custom tag names that describe the content they contain:
```XML
<sales_records>
{sales_records}
</sales_records>
```
The tag names don't need to follow any official XML specification - you're free to create descriptive names like sales_records, data, or records. More specific names generally work better than generic ones.
A Practical Example

Here's a clear example of why XML tags make a difference. In the "Not Great" version, it's unclear what content represents the buggy code versus the documentation:

The improved version uses XML tags to clearly separate the different types of content:

```Python
from datavortex import Pipeline, DataSource

def process_data(input_file, output_file):
    pipeline = Pipeline()
    source = DataSource.from_csv(input_file)
```

<docs>
# Creating a data source from data vortex
csv_source = DataSource.from_csv("data.csv")
</docs>

Now Claude can easily distinguish between the code that needs debugging and the documentation that should guide the debugging process.
Applying Structure to Your Prompts

Even when your interpolated content isn't massive, XML tags can still improve clarity. For example, when generating meal plans, you can group athlete information together:

<athlete_information>
- Height: {prompt_inputs["height"]}
- Weight: {prompt_inputs["weight"]}
- Goal: {prompt_inputs["goal"]}
- Dietary restrictions: {prompt_inputs["restrictions"]}
</athlete_information>

This makes it crystal clear to Claude that this block contains external input about the athlete that should inform the meal plan generation.
When to Use XML Tags

XML tags are most useful when:

    You're including large amounts of context or data
    Your prompt contains multiple distinct types of content
    You want to make the boundaries between different sections obvious
    You're interpolating content that might be confused with your instructions

While you might not see dramatic improvements with simple prompts, XML tags serve as delimiters that help Claude better understand your intent, leading to more consistent and accurate responses.

# 2025-08-05

继续昨天没做完的Anthropic Computer Use Demo的完整过程，包括AWS Bedrock集成


### 启动和测试

#### 6.1 启动Streamlit应用
```bash
source ~/.zshrc
export AWS_REGION=us-east-1
export API_PROVIDER=bedrock
streamlit run computer_use_demo/streamlit.py --server.port 8501
```

#### 6.2 访问界面
- URL: http://localhost:8501
- 配置: API Provider = bedrock, Model = anthropic.claude-3-5-sonnet-20240620-v1:0

## bugfix

### bug1: Python版本兼容性

**问题**: 项目使用了Python 3.11+的新特性，但系统Python为3.9.6

**解决方案**: 
- 使用pyenv管理Python版本
- 升级到Python 3.11.13
- 为旧版本添加兼容性代码

### bug2: AWS Bedrock权限配置

**问题**: IAM用户缺少Bedrock访问权限

**解决方案**:
- 添加AmazonBedrockFullAccess策略
- 在Bedrock控制台申请模型访问权限
- 测试不同区域的模型可用性

### bug3: 模型ID格式问题

**问题**: 不同区域和模型版本的ID格式不一致

**解决方案**:
- 创建测试脚本验证所有可能的模型ID
- 选择us-east-1区域的稳定模型
- 使用anthropic.claude-3-5-sonnet-20240620-v1:0

### bug4: 依赖包冲突

**问题**: 不同Python版本的包兼容性问题

**解决方案**:
- 使用pyenv创建干净的Python环境
- 重新安装所有依赖包
- 验证关键包的版本兼容性

## 工具系统详解

### Computer工具
负责桌面交互操作：
- 截屏 (`screenshot`)
- 鼠标操作 (`left_click`, `right_click`, `mouse_move`)
- 键盘输入 (`key`, `type`)
- 拖拽操作 (`left_click_drag`)

### Bash工具
执行命令行操作：
- 异步进程管理
- 命令超时控制
- 输出缓冲和格式化

### Edit工具
文件编辑功能：
- 文件查看 (`view`)
- 文件创建 (`create`)
- 字符串替换 (`str_replace`)
- 内容插入 (`insert`)
- 撤销操作 (`undo_edit`)

## 配置文件

### AWS配置 (~/.aws/config)
```ini
[default]
region = us-east-1
```

### Shell配置 (~/.zshrc)
```bash
export PATH="/usr/local/bin:$PATH"
export PYENV_ROOT="$HOME/.pyenv"
command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

## 测试验证

### 模型连接测试
```python
# 测试脚本验证Claude模型可用性
client = AnthropicBedrock()
response = client.messages.create(
    model="anthropic.claude-3-5-sonnet-20240620-v1:0",
    max_tokens=50,
    messages=[{"role": "user", "content": "Hello"}]
)
```

# 2025-08-04

继续Tool Use Computer Use， 今天主要是实操：

```
anthropic-quickstarts/
├── computer-use-demo/
│   ├── computer_use_demo/
│   │   ├── streamlit.py          # Web界面
│   │   ├── loop.py               # 核心API循环
│   │   └── tools/                # 工具系统
│   │       ├── computer.py       # 桌面交互工具
│   │       ├── bash.py           # 命令行工具
│   │       ├── edit.py           # 文件编辑工具
│   │       └── base.py           # 工具基类
│   ├── Dockerfile                # 容器配置
│   └── requirements.txt          # Python依赖
```
稍微记录下配置和运行Anthropic Computer Use Demo的完整过程，包括AWS Bedrock集成。
### 第一阶段：环境准备

#### 1.1  AWS配置
- 账户ID & access key
-  区域: us-east-1 
- 可用模型: anthropic.claude-3-5-sonnet-20240620-v1:0
AWS Bedrock: 通过AWS服务调用


#### 1.2 AWS CLI安装
```bash
# 安装AWS CLI和boto3
pip3 install awscli boto3
```

#### 1.3 AWS凭证配置
```bash
# 配置AWS访问密钥
aws configure set aws_access_key_id AKIA2ZD4IJUQXCHG6BUQ
aws configure set aws_secret_access_key [SECRET_KEY]
aws configure set default.region us-east-1
```

### 第二阶段：Python环境升级

#### 2.1 问题识别
初始Python版本3.9.6不支持项目所需的现代Python特性：
- `StrEnum` (Python 3.11+)
- `dataclass(kw_only=True)` (Python 3.10+)

#### 2.2 pyenv安装和配置
```bash
# 使用Homebrew安装pyenv
export PATH="/usr/local/bin:$PATH"
brew install pyenv

# 安装Python 3.11.13
pyenv install 3.11.13
pyenv global 3.11.13

# 配置shell环境
echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.zshrc
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
```

#### 2.3 验证升级
```bash
source ~/.zshrc
python --version  # 输出: Python 3.11.13
```

### 第三阶段：依赖安装

#### 3.1 安装项目依赖
```bash
cd computer-use-demo
pip install -r computer_use_demo/requirements.txt
```

#### 3.2 关键依赖包
- `streamlit==1.41.0`: Web界面框架
- `anthropic[bedrock,vertex]>=0.39.0`: Anthropic API客户端
- `boto3>=1.28.57`: AWS SDK
- `jsonschema==4.22.0`: JSON验证
- `google-auth<3,>=2`: Google认证

### 第四阶段：AWS Bedrock配置

#### 4.1 权限配置
在AWS控制台中为IAM用户添加权限：
- 策略：`AmazonBedrockFullAccess`
- 自定义权限：`bedrock:InvokeModel`, `bedrock:ListFoundationModels`

#### 4.2 模型访问申请
在AWS Bedrock控制台申请Claude模型访问权限：
- Claude 3.5 Sonnet
- Claude 3 Sonnet
- Claude 3 Haiku

#### 4.3 区域测试
测试不同AWS区域的模型可用性：
```bash
# us-west-2: 权限问题
# us-east-1: 成功找到可用模型
export AWS_REGION=us-east-1
```

# 2025-08-03

今天学习How Computer Use works。
Computer Use 和 Tool Use 在流程上几乎完全一样，但区别是：
这个“工具”不是传统功能模块，而是“一个虚拟电脑”接口。
Claude 不是调用 API，而是在“虚拟电脑”里：

    移动鼠标
    输入键盘内容
    点击按钮
    滚动页面
    截图反馈
 交互流程（几乎一样）：

    你告诉 Claude，它现在可以用“电脑”这个工具；
    Claude 判断问题需要手动操作网页或软件；
    Claude 发出一个如 click(x=500, y=300) 的请求；
    你运行的 Docker 环境模拟实际鼠标点击；
    Claude 等待截图、界面反馈，继续操作。
应用场景

    UI 自动化测试
    模拟用户操作网页或桌面应用
    演示或录制使用流程
    教学自动化：让 Claude 操作 Excel、Web App
核心差异一句话总结：
    Tool Use 是 Claude 使用你定义的“能力模块”，而 Computer Use 是 Claude 操作你提供的“虚拟手”。

维度	Tool Use	Computer Use
功能类型	调用 API / 函数	模拟用户使用计算机
接口形式	工具 schema + 参数	虚拟计算机操作 schema
响应方式	工具执行并返回结果	计算机动作 + 页面反馈
场景	数据处理、内容生成	Web 操作、流程演示
🏗️ 技术实现的关键点
Claude 自己不能直接动电脑

Claude 并不能真的动你的电脑，而是发送“意图请求”（比如点击、输入），由你本地环境（Docker 容器 + Firefox）来执行实际操作。
🧰 启动 Claude 的 Computer Use 的流程？

    安装 Docker；
    配置 AWS CLI（只是为了授权用，实际不涉及云部署）；
    克隆 Anthropic 提供的 quickstarts 仓库；
    启动容器，打开聊天界面和虚拟浏览器。
实际操作参见quick-start repo。
具体实操教程没有，只能自己摸一下了。

# 2025-08-02

https://www.promptingguide.ai/
### Prompt engineering 技术

### 详细性能比较矩阵

| 技术 | 准确性 | 可解释性 | 计算成本 | 实现复杂度 | 泛化能力 | 适用任务类型 |
|------|--------|----------|----------|------------|----------|--------------|
| Zero-shot | 中等 | 低 | 低 | 低 | 高 | 简单分类、基础QA |
| Few-shot | 高 | 中等 | 低 | 低 | 中等 | 大多数NLP任务 |
| CoT | 很高 | 高 | 中等 | 中等 | 高 | 推理、数学、逻辑 |
| Self-Consistency | 很高 | 高 | 高 | 中等 | 高 | 需要高准确性的推理 |
| ToT | 最高 | 最高 | 很高 | 高 | 中等 | 复杂规划、创意任务 |
| RAG | 很高 | 中等 | 高 | 高 | 高 | 知识密集型任务 |
| PAL | 很高 | 高 | 中等 | 中等 | 中等 | 数学、编程、计算 |
| ReAct | 高 | 很高 | 高 | 高 | 高 | 需要工具使用的任务 |

### 任务类型匹配指南

| 任务大类 | 具体任务 | 推荐技术组合 | 优先级 | 备选方案 | 适用场景说明 |
|----------|----------|--------------|--------|----------|--------------|
| **文本分类** | 简单分类 | Zero-shot → Few-shot | 高 | CoT（复杂类别） | 明确类别，充足训练数据 |
| | 复杂分类 | Few-shot + CoT | 高 | Self-Consistency | 需要推理的分类任务 |
| | 多标签分类 | Few-shot + 结构化输出 | 中 | Zero-shot + 格式约束 | 多个标签同时预测 |
| | 情感分析 | Few-shot | 高 | Zero-shot | 有代表性样本时 |
| **问答系统** | 事实性问答 | RAG + Few-shot | 高 | RAG + Zero-shot | 需要准确事实信息 |
| | 推理性问答 | CoT + Self-Consistency | 高 | ToT（复杂推理） | 多步逻辑推理 |
| | 开放性问答 | Few-shot + 创意提示 | 中 | Zero-shot + 角色设定 | 主观性强的问题 |
| | 常识问答 | Generate Knowledge + CoT | 中 | Few-shot + CoT | 需要背景知识 |
| **数学逻辑** | 基础计算 | PAL | 高 | CoT + 验证 | 数值计算为主 |
| | 复杂推理 | CoT + Self-Consistency | 高 | ToT | 多步数学推理 |
| | 多步骤问题 | ToT | 高 | CoT + 分解 | 需要探索多种路径 |
| | 几何问题 | Multimodal CoT | 中 | CoT + 图形描述 | 涉及图形理解 |
| **创意生成** | 内容创作 | Few-shot + 创意提示 | 高 | Zero-shot + 风格指导 | 需要特定风格 |
| | 头脑风暴 | ToT | 高 | Few-shot + 多样性 | 需要多种创意方案 |
| | 风格迁移 | Few-shot + 风格示例 | 高 | Zero-shot + 详细描述 | 有明确目标风格 |
| | 故事创作 | Few-shot + 结构化 | 中 | CoT + 情节规划 | 长篇内容创作 |
| **代码任务** | 代码生成 | Few-shot + 注释示例 | 高 | Zero-shot + 详细需求 | 有类似代码参考 |
| | 代码解释 | CoT | 高 | Few-shot + 示例 | 需要逐步解析 |
| | 调试辅助 | ReAct + 工具集成 | 高 | CoT + 错误分析 | 需要执行和测试 |
| | 代码重构 | Few-shot + 最佳实践 | 中 | CoT + 优化建议 | 有重构标准 |
| **信息提取** | 命名实体识别 | Few-shot | 高 | Zero-shot + 格式化 | 有标注示例 |
| | 关系抽取 | Few-shot + 结构化 | 高 | CoT + 推理 | 复杂关系识别 |
| | 文档解析 | RAG + Few-shot | 中 | Multimodal CoT | 结构化文档 |
| | 数据清洗 | Few-shot + 规则 | 中 | PAL + 验证 | 有清洗规则 |
| **对话系统** | 任务导向对话 | ReAct + 工具集成 | 高 | Few-shot + 状态管理 | 需要执行操作 |
| | 闲聊对话 | Few-shot + 个性化 | 中 | Zero-shot + 角色设定 | 社交性对话 |
| | 客服对话 | RAG + Few-shot | 高 | Few-shot + 知识库 | 需要准确信息 |
| | 教学对话 | CoT + 个性化 | 高 | Few-shot + 教学法 | 需要解释过程 |

### 选择决策树

```
任务复杂度评估
├── 简单任务 (单步骤，明确答案)
│   ├── 有示例数据 → Few-shot
│   └── 无示例数据 → Zero-shot
├── 中等复杂度 (多步骤，需要推理)
│   ├── 需要外部知识 → RAG
│   ├── 数学/计算类 → PAL
│   └── 一般推理 → CoT
└── 高复杂度 (需要探索，多种可能)
    ├── 需要工具使用 → ReAct
    ├── 需要最优解 → ToT
    └── 需要高准确性 → Self-Consistency
```

# 2025-08-01

回到prompt evaluation 章节，因为看到
superclaude这个项目https://github.com/NomenAK/SuperClaude.git，之前感觉很好用，今天想起看看源码，居然全是md文件。SuperClaude给我感受到AI Agent开发的一个重要趋势： 文档驱动的智能体定义。
这种YAML前置元数据 + Markdown内容的模式，实际上是在声明式地定义智能体的能力和行为。
想想一个项目里面里面全是定义，但这个"定义"包含多个层面：

1. 行为定义（最重要）
智能体的目标和原则
决策优先级
交互模式
2. 能力定义
可用的工具和资源
权限和约束
集成的外部服务
3. 协作定义
与其他智能体的关系
协作模式和通信协议
冲突解决机制
4. 上下文定义
领域知识和专业背景
记忆和状态管理
学习和适应能力

所以重新看prompt evaluation, 也是workflow的定义。

此外，交叉学习一些其他的教程，更好的理解prompt
https://datawhalechina.github.io/llm-universe/#/
这里更直接的罗列一些prompt engineering的技巧。

https://www.53ai.com/news/tishicikuangjia/2025062727685.html
解释prompt engineering 和 context engineering 的区别，当然我很喜欢那个交叉饼图。对于RAG和promt


再挖一个坑吧，反正要躺的：
https://github.com/dair-ai/Prompt-Engineering-Guide
这是一个60k star的 专门prompt engineering 的repo。

# 2025-07-29

今天学习量稍微少一点，累了。

Done:
学习RAG，简单的练习，chunk还是structured， semantic 太慢了。

RAG pipeline 总结的真好:

    Chunk source documents
    Generate embeddings for each chunk
    Store embeddings in a vector database
    When a user asks a question, embed their query
    Find the most similar stored embeddings using cosine similarity
    Add the relevant chunks to a prompt with the user's question
    Send the enhanced prompt to Claude for a response
实践方面，比较了一下向量数据库，练手还是从chroma开始，也是Cosine + filtering的检索方式。后续学习再看Milvus。
哇，后面练习量好大，今天先休息吧。
明天继续RAG

# 2025-07-28

https://www.anthropic.com/engineering/built-multi-agent-research-system
重点看了一下agent部分，记录一下扩展部分:
1. https://github.com/humanlayer/12-factor-agents，  演讲视频： https://www.youtube.com/watch?v=8kMaTybvDUw   不要太依赖framework，尤其langchain的相当多功能不稳定。直接function call就很高效。

2. https://huggingface.co/learn/agents-course/en/这个讲的真是好，推荐。感觉有填不完的坑。
 https://huggingface.co/agents-course/notebooks/blob/main/unit1/dummy_agent_library.ipynb 

3. 对于agent框架，可以从crewAI或者Smolagent 开始, 然后对multi-agent可以学习 AutoGen， 最后再学习langchain/langgraph. 不然淹死，哈哈。
4. RAG 数据库的种类也好多，切片的策略回头继续研究，又是坑要填。

# 2025-07-27

Done:
Claude with the Anthropic API 中几种Agents and workflows中，chaining是专门处理复杂任务的, routing处理不同请求要不同定制化需求的, parrellelization  的prompt可能没那么大。

而CC 仅仅用这些简单的命令就 完成agent所需要的工具集，真是Unix哲学大师啊
    bash - Run any command
    read - Read any file
    write - Create any file
    edit - Modify files
    glob - Find files
    grep - Search file contents

Workflows vs agents 对比也非常清晰： agents灵活，但成功率低。

字节刚开源coze studio，是一个相当不错的消息，不过进一步挖掘claude code的sub agent的潜力才是我主要应该关注的。

# 2025-07-26

Done:
完成Claude with the Anthropic API 里的Claude Code and computer use部分，因为对claude code感兴趣，先看了这部分。 对于可以debug production 部署的环境问题，还是挺惊讶啊
Computer use 部分没有完全理解最合适的应用场景。
workflow 可能应用的会比 agent要少一点吧。更倾向于n8n 转换。
Environment inspection部分，不够细，需要自己再查一下其他视频。 
同时关于Parallelization workflows， 其实和Parallelization claude code 用worktree完全不同，Parallelization workflows只是拆解任务。但因为claude的上下文长度的限制，对于subtask，共享上下文的冲突，多少会引入一些问题。 期待更优解。
今天先到 这里。
又回来了，刚才查了一下，貌似sub agent就是不错的对上面问题的解，因为subagent，独立管理上下文，也不干扰main agent，到最后总结才更有效。而且sub agent作为专家团队的作用可以有更多应用。

# 2025-07-25

### Done:
Parallelizing Claude Code 用git worktree,还可以定制command 来create worktree和merge，真实用。
今天浏览了一下Google Cloud Vertex的功能，再对比了amazon bedrock， 发展真快，以前的mlOps 现在都统一了。
简单试了下claude code 集成github action, commit/PR/merge 各种飞起。
简单看完claude code hook，脑袋空白，觉得有用，但还没试
Todo:
明天试试claude code hook.

# 2025-07-24

Done：
因为对Claudle code 感兴趣，先学习Claude Code in Action前5个视频，配置了uigen的项目并 简单跟做练习。比如命令/init， /compact, /clear, 新开一个chat原来是clear, 

感受：
1. 看视频比想象要慢，半小时视频，稍微加点练习就超过一个小时了。要坚持一个月不容易啊，大家加油。
2. 优化自己的Claude.md是细活，要慢慢来。
3. 刚知道think mode 还有这么多区别：
    "Think" - Basic reasoning
    "Think more" - Extended reasoning
    "Think a lot" - Comprehensive reasoning
    "Think longer" - Extended time reasoning
    "Ultrathink" - Maximum reasoning capability
4. 这个打卡做的也不错，markdown确实更方便。

ToDolist：
1. 由于没有AWS Bedrock or Google Cloud Vertex的账号，回头要花点时间弄一下。
2. 错过开营，找机会看回放。
3. 感觉学习笔记以后可以用claude code辅助一下，因为练习的记录都在。
4. 不懂prisma数据库，todo了。


<!-- Content_END -->
