# 一、目标定义

你未来要具备的不是“会调模型 API”，而是以下能力：

- 基于 OpenAI / Claude / Gemini / Qwen / DeepSeek / 本地模型 搭建统一模型层
- 设计 Agent Runtime / Workflow Runtime
- 开发 Tool / Skill / Manifest 机制
- 接入 RAG / Index / Retrieval
- 做规则约束、日志追踪、评估回归
- 做多轮优化：效果、成本、稳定性、安全

所以你的目标系统应该长成这样：

```text
User / API / UI
    ↓
Agent / Workflow Runtime
    ↓
Planner / Router / State Manager
    ↓
LLM Adapter Layer
    ↓
Tools / Skills / Retrieval / External Systems
    ↓
Observability / Eval / Storage
```

---

# 二、架构设计：最小可扩展 Agent 系统

下面这个架构，不追求“最复杂”，而是追求：

- 可自己实现
- 可逐步增强
- 可替换模型/工具/检索组件
- 可迁移到真实业务

---

## 1. 总体模块划分

建议目录结构：

```text
agent_platform/
├── app/
│   ├── api/                  # FastAPI 对外接口
│   ├── cli/                  # 调试/本地运行命令
│   └── ui/                   # 可选，后续加前端
│
├── core/
│   ├── llm/                  # 模型适配层
│   ├── prompts/              # prompt / rules / templates
│   ├── schemas/              # pydantic/json schema
│   ├── runtime/              # agent/workflow runtime
│   ├── tools/                # 工具注册与执行
│   ├── skills/               # skill 抽象层
│   ├── retrieval/            # RAG / indexing / search
│   ├── memory/               # context / state / conversation / cache
│   ├── guardrails/           # 规则、安全、校验
│   ├── evals/                # 评估体系
│   ├── tracing/              # 日志、埋点、trace
│   └── config/               # 配置管理
│
├── workflows/
│   ├── testcase_generation/
│   │   ├── manifest.yaml
│   │   ├── workflow.yaml
│   │   ├── prompts/
│   │   ├── examples/
│   │   └── tests/
│   ├── requirement_analysis/
│   └── meeting_summary/
│
├── tools/
│   ├── http_tools/
│   ├── file_tools/
│   ├── db_tools/
│   ├── search_tools/
│   └── custom_tools/
│
├── skills/
│   ├── requirement_skill/
│   ├── testcase_skill/
│   └── report_skill/
│
├── data/
│   ├── docs/
│   ├── indexes/
│   ├── eval_sets/
│   └── cache/
│
├── tests/
├── scripts/
├── docs/
├── docker/
├── pyproject.toml
└── README.md
```

---

## 2. 每个核心模块的职责

---

### A. `core/llm/`：模型适配层

目标：统一接不同厂商模型。

建议抽象：

```python
class LLMProvider(Protocol):
    async def generate(self, messages, **kwargs) -> LLMResponse: ...
    async def structured_generate(self, messages, schema, **kwargs) -> dict: ...
    async def tool_call(self, messages, tools, **kwargs) -> ToolCallResult: ...
```

建议实现：
- `openai_provider.py`
- `anthropic_provider.py`
- `gemini_provider.py`
- `openai_compatible_provider.py`

必须支持：
- 普通聊天生成
- 结构化输出
- tool calling
- 流式输出
- 多模型 fallback
- 参数统一封装

你未来业务逻辑不能直接依赖某个厂商 SDK。

---

### B. `core/prompts/`：Prompt / Rule / Context 模板层

职责：
- 管理 prompt 模板
- 管理 rules
- 动态拼接上下文
- 版本化

建议结构：
```text
prompts/
├── planner/
├── router/
├── writer/
├── reviewer/
├── extractor/
└── rules/
```

建议做法：
- prompt 与代码分离
- 模板参数化
- 支持环境/版本切换
- 引入 system / developer / task prompt 分层

---

### C. `core/schemas/`：统一输入输出结构

职责：
- 所有 LLM 输出、tool 参数、workflow state 都结构化
- 用 Pydantic + JSON Schema 做强约束

例如：
- `ToolSpec`
- `ToolCall`
- `WorkflowState`
- `TaskPlan`
- `RetrievedChunk`
- `EvalResult`

这是系统稳定性的关键。

---

### D. `core/runtime/`：Agent / Workflow 运行时

这是核心中的核心。

建议先分两层：

#### 1）Workflow Runtime
偏确定性流程：
- 节点定义
- 节点依赖
- 条件分支
- 中间状态
- 重试/超时/回退

#### 2）Agent Runtime
偏动态决策：
- planner
- router
- tool selection
- critic/reviewer
- self-refine

建议最小抽象：

```python
class Node:
    name: str
    async def run(self, state: WorkflowState) -> WorkflowState: ...

class WorkflowExecutor:
    async def execute(self, workflow: Workflow, initial_state: dict) -> WorkflowState: ...
```

支持的节点类型：
- `LLMNode`
- `ToolNode`
- `RetrieverNode`
- `RouterNode`
- `ValidatorNode`
- `HumanReviewNode`
- `MergeNode`

---

### E. `core/tools/`：工具层

职责：
- 注册工具
- 暴露 schema
- 执行工具
- 错误处理
- 返回标准结构

你应该把工具分类型：
- HTTP API Tool
- DB Tool
- File Tool
- Search Tool
- Code Tool（谨慎）
- Internal Service Tool

建议抽象：
```python
class BaseTool:
    name: str
    description: str
    input_schema: dict

    async def run(self, **kwargs) -> ToolResult:
        ...
```

并做一个 `ToolRegistry`。

---

### F. `core/skills/`：技能层

职责：
- 对业务能力做更高层抽象
- skill 可以组合多个 tool / prompt / retrieval

比如：
- `generate_testcase_skill`
- `summarize_requirement_skill`
- `search_policy_skill`

区别：
- tool 偏原子执行
- skill 偏业务能力组合

---

### G. `core/retrieval/`：检索与索引层

职责：
- 文档切分
- embedding
- 建索引
- 检索
- rerank
- context packing

建议最小功能：
- 文档 loader
- chunker
- embedding provider
- vector store
- retriever

建议先用：
- Chroma / FAISS（本地轻量）
后续再换：
- Milvus / pgvector / Elasticsearch 混合检索

---

### H. `core/memory/`：状态与记忆层

职责：
- workflow state
- conversation context
- 短期缓存
- 工具返回缓存
- 用户会话态

注意：
你现在不需要一开始做复杂长期记忆，但要做：
- 当前任务上下文
- 上一步输出
- 可追踪中间变量

---

### I. `core/guardrails/`：规则与安全层

职责：
- 输入校验
- 输出校验
- 工具调用限制
- 风险操作拦截
- prompt injection 基础防护
- 敏感信息脱敏

建议分三层：
1. pre-check
2. runtime-check
3. post-check

---

### J. `core/tracing/`：可观测性

职责：
- 记录每一步：
  - prompt
  - model
  - tokens
  - latency
  - tool calls
  - retrieval hits
  - outputs
- 支持单次运行回放

建议每次运行生成 `trace_id`。

---

### K. `core/evals/`：评估体系

职责：
- 样本集管理
- 单点评测
- 端到端评测
- prompt / workflow / model 对比

你未来优化 Agent，必须依赖它。

---

## 3. Workflow 配置设计建议

你可以用 YAML 做 workflow 配置：

```yaml
name: testcase_generation
version: 0.1.0

inputs:
  - requirement_text
  - metadata

nodes:
  - id: extract_requirements
    type: llm
    prompt: extractor/requirement_extractor.jinja2
    output_schema: RequirementSpec

  - id: retrieve_history_cases
    type: retriever
    source: testcase_index
    input_from: extract_requirements.keywords

  - id: generate_testcases
    type: llm
    prompt: writer/testcase_generator.jinja2
    context_from:
      - extract_requirements
      - retrieve_history_cases
    output_schema: TestcaseList

  - id: review_testcases
    type: llm
    prompt: reviewer/testcase_review.jinja2
    input_from: generate_testcases
    output_schema: ReviewResult

outputs:
  - review_testcases
```

这样可以做到：
- 配置驱动
- 易扩展
- 易版本管理
- 易调试

---

## 4. Manifest 设计建议

建议 skill / workflow / tool 都有最小 manifest。

### Tool Manifest
```yaml
name: get_requirement_metadata
description: Query requirement metadata by ID
version: 1.0.0
input_schema:
  type: object
  properties:
    req_id:
      type: string
  required: [req_id]
output_schema:
  type: object
  properties:
    title:
      type: string
    owner:
      type: string
    status:
      type: string
timeout: 10
retry: 2
side_effects: false
```

### Skill Manifest
```yaml
name: testcase_generation_skill
description: Generate test cases from requirement text and related context
version: 1.0.0
depends_on:
  - get_requirement_metadata
  - search_similar_testcases
inputs:
  - requirement_text
outputs:
  - testcase_list
```

---

## 5. 第一版最小系统应该包含什么

别一开始追求大而全，第一版建议只做：

### 必备
- 模型适配层（至少 2 家）
- 结构化输出
- tool registry
- 一个简单 workflow runtime
- 一个 retriever
- 基础 trace
- 基础 eval

### 不必一开始就做
- 多 agent 协作
- 复杂长期 memory
- 可视化流程编排 UI
- 权限系统的完整 RBAC
- 复杂 DAG 编辑器

---

# 三、学习路线：按“能搭出来”为目标

你 Python 很强，所以路线按**系统设计 + 实战实现**走，不按入门课程走。

---

# 阶段一：建立 Agent 系统底座（第 1~3 周）

目标：独立搭一个最小运行闭环。

---

## 学什么

### 1. LLM API 统一封装
重点：
- OpenAI / Anthropic / OpenAI-compatible
- 普通生成
- 结构化输出
- tool calling

### 2. Pydantic / JSON Schema
重点：
- 输入输出结构化
- schema 驱动调用
- 校验与报错

### 3. Prompt / Context 模板化
重点：
- 模板参数化
- 多层 prompt
- 输出约束

### 4. Tool 抽象
重点：
- 工具注册
- schema 暴露
- 标准结果返回

---

## 本阶段产出
做一个最小 demo：

**需求分析 Agent**
- 输入需求文本
- 提取关键字段
- 调一个查询 metadata 的 tool
- 输出结构化需求摘要

---

# 阶段二：实现 Workflow Runtime（第 4~6 周）

目标：能串联多个步骤，不靠手写 if-else 逻辑拼凑。

---

## 学什么

### 1. Workflow Runtime 设计
重点：
- node / edge / state
- 节点执行器
- 条件分支
- 中间变量

### 2. 常见节点类型
- LLMNode
- ToolNode
- RetrieverNode
- ValidatorNode

### 3. 错误与回退
- 超时
- 重试
- fallback
- 人工审核点预留

---

## 本阶段产出
做一个 workflow：

**testcase_generation_v1**
1. 提取需求点
2. 检索相似历史用例
3. 生成测试用例
4. 评审格式和完整性
5. 输出最终 testcase

---

# 阶段三：补 RAG / Index / Retrieval（第 7~9 周）

目标：系统具备知识能力，而不是只靠 prompt 硬猜。

---

## 学什么

### 1. 文档处理
- 清洗
- chunk
- metadata

### 2. 检索
- embedding
- top-k
- rerank
- context packing

### 3. RAG 与 workflow 结合
- 什么节点检索
- 检索结果怎么注入 prompt
- 何时必须引用来源

---

## 本阶段产出
做一个：
**测试规范/历史测试库 RAG**
- 导入测试规范文档
- 检索相似 testcase / 规范片段
- 生成结果带来源引用

---

# 阶段四：补规则、可观测、评估（第 10~12 周）

目标：让系统可优化，而不是“能跑就算了”。

---

## 学什么

### 1. Guardrails
- 输出格式校验
- 风险操作限制
- 没证据不下结论

### 2. Tracing
- 每一步调用日志
- token、耗时、结果

### 3. Eval
- 构造 benchmark 数据集
- 对比 prompt / model / workflow
- 做回归测试

---

## 本阶段产出
做一个评估体系：

### 对 testcase_generation 的评估维度
- 格式合规率
- 字段完整率
- 边界覆盖率
- 重复率
- 人工满意度

---

# 阶段五：做平台化抽象（第 13~16 周）

目标：从“一个项目”升级到“一个可以承载多个业务 workflow 的框架”。

---

## 学什么

### 1. Skill / Manifest / Registry
- 技能注册
- 自动发现
- schema 管理

### 2. 多模型路由与成本优化
- 小模型做分类、抽取
- 大模型做复杂生成
- fallback

### 3. 工程化
- Docker
- 配置管理
- 单元测试
- e2e 测试
- CI

---

## 本阶段产出
把系统升级为：
- 可新增 workflow
- 可新增 tool
- 可新增 skill
- 可新增评估集
- 可切换模型

---

# 四、90 天执行计划

下面这版更“落地”。

---

## 第 1 个月：搭底座

### Week 1
- 统一模型层
- 接 OpenAI + 1 个兼容模型
- 支持 basic generate / structured output

### Week 2
- 工具注册层
- 3 个基础 tool
- prompt 模板管理

### Week 3
- 最小 workflow runtime
- 支持 3 类节点：LLM / Tool / Validator

### Week 4
- 做第一个业务 workflow：需求提取 or testcase 生成简版

---

## 第 2 个月：补知识与评估

### Week 5
- 文档 loader + chunk + embedding

### Week 6
- vector search + retrieval node

### Week 7
- 把检索接入 testcase_generation workflow

### Week 8
- trace + run logging + 可回放

---

## 第 3 个月：补规则与平台化

### Week 9
- rule / guardrails
- 输出后校验、自动纠错

### Week 10
- 构建评估数据集
- 跑 benchmark

### Week 11
- skill manifest / registry
- skill 组合模式

### Week 12
- Docker 化
- 写文档
- 打磨成可复用框架

---

# 五、资料清单：按优先级筛过的

我只给你对这个目标最有用的，不给泛滥清单。

---

## A. 模型与 Tool Calling

### 1. OpenAI 官方文档
重点看：
- Responses / Chat API
- Structured Outputs
- Function Calling / Tools

### 2. Anthropic 官方文档
重点看：
- Tool Use
- Prompting
- System prompt / output control

### 3. Google Gemini 官方文档
重点看：
- Function calling
- JSON 输出
- 多模态输入（后续可用）

### 4. OpenAI-compatible 生态
例如 vLLM / Ollama / LiteLLM 文档  
重点是理解“统一适配层”怎么做。

---

## B. Workflow / Agent 设计

### 1. LangGraph 文档
重点不是学框架 API，而是学：
- state graph
- node
- edge
- conditional routing
- agent workflow 设计方法

### 2. LangChain 文档
重点看：
- tool
- retriever
- structured output
- agent basics

### 3. Semantic Kernel 文档
重点看：
- skill/function 抽象
- planner 思想

### 4. LlamaIndex 文档
重点看：
- indexing
- retrieval
- workflow / agent 结合

---

## C. RAG / Retrieval

### 必看内容
- chunking 策略
- reranking
- metadata filtering
- citation/grounding

### 推荐方向
- LlamaIndex RAG tutorials
- LangChain retrieval docs
- Chroma / FAISS 入门
- pgvector / Milvus 了解

---

## D. Eval / Observability

### 1. LangSmith
看概念，不一定必须用
重点学习 trace、run、dataset、comparison 思路

### 2. Promptfoo
非常适合快速做 prompt/workflow 对比评测

### 3. OpenAI Evals
看设计思想即可

### 4. Arize Phoenix
了解 AI observability 思路即可

---

## E. Guardrails / Security

### 1. OWASP for LLM Applications
重点了解：
- prompt injection
- data leakage
- unsafe tool usage

### 2. Guardrails AI / structured validation 相关资料
重点学思想，不一定要依赖库

---

## F. 工程化

### 1. FastAPI 官方文档
你大概率会用它做服务层

### 2. Pydantic 官方文档
结构化输出和 schema 核心依赖

### 3. Docker 官方文档
部署与环境隔离必备

### 4. pytest / httpx / asyncio
做测试和异步执行很有用

---

# 六、建议的技术选型

为了快速落地，我建议你第一版这样选：

---

## 模型层
- OpenAI / OpenAI-compatible
- Anthropic（可选）
- LiteLLM（可作为适配参考或直接用）

## 服务层
- FastAPI

## 数据结构
- Pydantic v2

## 检索层
- Chroma / FAISS（本地）
- 后期 pgvector / Milvus

## 模板
- Jinja2

## 存储
- SQLite 起步
- 后期 PostgreSQL

## 追踪
- 先自己打 JSON log
- 后期接 LangSmith / Phoenix

## 评估
- Promptfoo + 自定义 pytest benchmark

---

# 七、你最应该做的 3 个实战项目

这 3 个项目能把你的能力栈拉满。

---

## 项目 1：Requirement Analyzer
目标：
- 输入需求文档
- 提取结构化需求点
- 调元数据查询 tool
- 输出标准摘要

锻炼：
- structured output
- tool calling
- prompt/rule 设计

---

## 项目 2：Testcase Generation Agent
目标：
- 输入需求
- 检索规范和历史 testcase
- 分阶段生成 testcase
- reviewer 检查质量
- 输出标准格式

锻炼：
- workflow runtime
- retrieval
- multi-step generation
- eval

---

## 项目 3：Meeting / Ticket / Report Automation Agent
目标：
- 输入会议记录 / 工单 / issue
- 自动总结
- 自动提取 action items
- 自动创建任务 / 发送通知

锻炼：
- tool integration
- workflow 编排
- 业务自动化闭环

---

# 八、执行中的关键原则

---

## 1. 先做“最小可运行闭环”
不要一开始就想做大而全平台。

先跑通：
- 模型
- tool
- workflow
- trace
- eval

---

## 2. 所有关键步骤都结构化
不要依赖纯文本中间结果。

---

## 3. LLM 只做它擅长的事
- 分类、提取、生成、总结、评审：给 LLM
- 精确规则、流程控制、状态管理、权限判断：交给代码

---

## 4. 先 workflow，后 agent
固定流程更稳定，更适合搭底座。  
动态 agent 放在后面增强。

---

## 5. 评估能力要尽早建立
没有 eval，就没有真正的优化。

---

# 九、你接下来可以直接开工的顺序

如果你今天就开始，我建议你这样干：

### 第一步
搭一个 `core/llm/`，统一接 2 个模型

### 第二步
搭 `core/tools/`，做 3 个简单工具

### 第三步
搭 `core/runtime/`，支持：
- LLMNode
- ToolNode
- ValidatorNode

### 第四步
做一个最小 workflow：需求提取

### 第五步
补 `core/retrieval/`，接一个本地索引

### 第六步
做 testcase generation workflow

### 第七步
补 `tracing + evals + guardrails`

---

# 十、学习/建设顺序表

按优先级排序：

1. 模型适配层
2. Structured Output / Schema
3. Tool Calling 与工具注册
4. Workflow Runtime
5. Prompt / Context Engineering
6. RAG / Retrieval
7. Skill / Manifest
8. Guardrails
9. Tracing
10. Eval
11. 成本/性能优化
12. 工程化与部署

---
