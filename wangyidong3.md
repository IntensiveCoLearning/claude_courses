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

#### 文本分类任务
- **简单分类**：Zero-shot → Few-shot
- **复杂分类**：Few-shot + CoT
- **多标签分类**：Few-shot + 结构化输出

#### 问答系统
- **事实性问答**：RAG + Few-shot
- **推理性问答**：CoT + Self-Consistency
- **开放性问答**：Few-shot + 创意提示

#### 数学和逻辑推理
- **基础计算**：PAL
- **复杂推理**：CoT + Self-Consistency
- **多步骤问题**：ToT

#### 创意生成
- **内容创作**：Few-shot + 创意提示
- **头脑风暴**：ToT
- **风格迁移**：Few-shot + 风格示例

#### 代码相关任务
- **代码生成**：Few-shot + 注释示例
- **代码解释**：CoT
- **调试辅助**：ReAct + 工具集成

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
