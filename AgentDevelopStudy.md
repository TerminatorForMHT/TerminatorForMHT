
# 一、总目标：到底要学成什么样

要达到的不是“会调 API”，而是能独立做出这种能力：

- 基于 GPT / Claude / Qwen / DeepSeek 等现有大模型
- 设计一套 **Agent 仓库结构**
- 维护：
  - `AGENTS.md`
  - `rules/`
  - `skills/`
  - `agents/`
  - `workflow/`
  - `tools/`
  - `docs/`
- 能把这些资产同步/发布到：
  - IDE AI 插件
  - Copilot
  - Claude Code
  - 企业内部 Agent 平台
- 能做到：
  - 结构化输入（manifest）
  - 工作流执行（workflow）
  - 工具调用（pytest / shell / indexer）
  - 状态管理（checkpoint / transcript / result）
  - 失败恢复

---

# 二、建议学习周期：12 周路线图

如果是软件开发基础还可以的人，按 **10~12 周** 比较合适。  
如果基础弱一些，可以放大到 **4~6 个月**。

---

# 三、学习计划总览

---

## 阶段 0：准备基础能力（1 周）

### 学习目标
把后续做 Agent 需要的基本工程能力补齐。

### 要学内容
- Python 基础工程能力
- Git / GitHub / 子模块 / symlink 基础
- 命令行工具开发
- JSON / YAML / Markdown

### 学习资料

#### Python
- 官方教程  
  https://docs.python.org/3/tutorial/
- Real Python（非常适合补工程化）  
  https://realpython.com/

重点掌握：
- `pathlib`
- `subprocess`
- `argparse` 或 `typer`
- `logging`
- `json`
- `yaml`（PyYAML）

#### Git
- Pro Git  
  https://git-scm.com/book/en/v2
- GitHub Docs  
  https://docs.github.com/

重点掌握：
- branch / rebase / cherry-pick
- submodule
- symlink 基础

#### YAML / JSON
- YAML 官方  
  https://yaml.org/
- JSON Schema 官方  
  https://json-schema.org/

---

## 阶段 1：大模型应用基础（1~2 周）

### 学习目标
理解：**Agent 不是训练模型，而是工程化使用模型**。

### 要学内容
- LLM 基础原理（不需要太深）
- Prompt Engineering
- Context Window / Token
- Tool Calling / Function Calling
- Structured Output
- RAG 基础
- LLM 常见缺陷：幻觉、上下文污染、指令漂移

### 推荐资料

#### 入门理解 LLM
- OpenAI 官方文档  
  https://platform.openai.com/docs
- Anthropic 官方文档  
  https://docs.anthropic.com/
- Google Gemini Docs  
  https://ai.google.dev/

#### Prompt Engineering
- OpenAI Prompt Engineering Guide  
  https://platform.openai.com/docs/guides/prompt-engineering
- Anthropic Prompting Guide  
  https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview
- DAIR.AI Prompt Engineering Guide  
  https://www.promptingguide.ai/

#### Tool Calling / Structured Output
- OpenAI Function Calling / Structured Outputs 文档
- Anthropic Tool Use 文档

建议重点看：
- system prompt 怎么写
- 输出 JSON / schema
- 工具调用协议
- 什么时候用 prompt，什么时候用工具

### 实战作业
做一个最小 demo：

```bash
python llm_cli.py --task "给生成一个 python 函数"
```

要求：
- 能读取 system prompt
- 能调用模型
- 能输出结果到文件

---

## 阶段 2：Prompt 系统设计与 Context Engineering（1~2 周）

### 学习目标
学会把 prompt 体系化，而不是“一个大提示词走天下”。

### 要学内容
- system prompt / developer prompt / task prompt 分层
- few-shot 示例设计
- 上下文选择
- 文件摘要
- 代码索引
- 动态上下文拼装
- 减少 token 浪费

### 推荐资料

#### Context Engineering / RAG
- LlamaIndex Docs  
  https://docs.llamaindex.ai/
- LangChain Docs（主要看 retrieval/context 部分）  
  https://python.langchain.com/
- Anthropic 关于长上下文与 prompt 设计的文档

#### 代码理解 / 代码检索方向
- Sourcegraph Cody 的一些博客和设计文章
- Continue.dev 文档  
  https://docs.continue.dev/
- Aider 的仓库与文档  
  https://github.com/Aider-AI/aider

重点观察：
- 它们如何选择相关文件
- 如何压缩上下文
- 如何生成 edit plan

### 实战作业
做一个 `context_builder.py`：

输入：
- 用户任务
- 项目根目录

输出：
- 相关文件列表
- 文件摘要
- 拼好的 prompt context

---

## 阶段 3：编码 Agent 基础版（2 周）

### 学习目标
做出一个能“读代码 -> 改代码 -> 跑测试 -> 再修复”的基本 agent。

### 要学内容
- ReAct / Plan-and-Execute 思路
- 工具调用
- 文件修改策略
- 测试执行
- 错误分析
- 多轮修复循环

### 推荐资料

#### Agent 思路
- ReAct 论文  
  https://arxiv.org/abs/2210.03629
- Plan-and-Solve / Tree-of-Thought 可选了解
- OpenAI Agents SDK 文档
- PydanticAI 文档  
  https://ai.pydantic.dev/

#### 编码 Agent 产品/项目
建议重点研究这些开源项目的设计，而不是只会使用：
- Aider  
  https://github.com/Aider-AI/aider
- Continue  
  https://github.com/continuedev/continue
- OpenHands（原 OpenDevin）  
  https://github.com/All-Hands-AI/OpenHands
- Cline / Roo Code（研究思路）
- Cursor（看产品能力设计）

重点学习：
- 编辑文件如何做
- shell / test 工具如何接
- 如何做确认/审查/日志

### 实战作业
做一个最小编码 agent：

```bash
python code_agent.py --task "为 utils/date.py 补充单元测试"
```

要求：
- 自动找相关文件
- 修改文件
- 调用 pytest
- 失败时读取日志给模型做一次修复
- 记录 transcript

---

## 阶段 4：Agent 资产工程化（1~2 周）

### 学习目标
从“一个 Python 脚本”升级到“一个 Agent 仓库”。

### 要学内容
- `AGENTS.md` 设计
- `rules/`、`skills/`、`agents/` 分层
- prompt 资产版本化
- markdown 驱动 agent
- docs 体系建设

### 推荐资料

这一块没有统一权威教程，建议“看成熟产品的约定 + 自己抽象”。

重点参考：
- Anthropic 关于 Claude Code / project instructions 的文档
- GitHub Copilot 自定义指令文档
- Continue 的 config / prompt 组织方式
- Cursor rules 的社区实践
- Aider 的 config / conventions

### 要输出的目录结构示例

```text
my_agent/
├── AGENTS.md
├── docs/
├── agent_src/
│   ├── rules/
│   ├── skills/
│   ├── agents/
│   └── templates/
└── workflow/
```

### 实战作业
把前一阶段的 `code_agent.py` 重构成：

- `rules/coding-style.md`
- `rules/repo-conventions.md`
- `skills/context-builder.md`
- `skills/code-writer.md`
- `skills/failure-analyzer.md`
- `AGENTS.md`

---

## 阶段 5：Workflow 与状态机（2 周）

### 学习目标
做出和示例类似的 **workflow 驱动执行系统**。

### 要学内容
- workflow 设计
- 状态机
- checkpoint
- resume
- case-by-case 执行
- partial / full 模式
- result / transcript / runtime state

### 推荐资料

#### Workflow / Graph
- LangGraph 文档  
  https://langchain-ai.github.io/langgraph/
- Temporal（理解工作流思想，哪怕不用）  
  https://temporal.io/
- Prefect / Airflow（理解任务编排思想）
- 状态机基础知识（任意资料都可以）

重点不是记框架 API，而是理解：
- 一个复杂任务如何拆 step
- step 之间怎么传状态
- 出错时怎么恢复
- 如何避免重复执行

### 实战作业
做一个 `workflow/demo-generation/`：

```text
workflow/
  demo-generation/
    workflow.md
    workflow-partial.md
    manifests/
      demo.yaml
    tools/
      workflow_engine.py
      validate_manifest.py
      run_tests.py
      parse_logs.py
```

要求：
- 能从 YAML manifest 批量处理任务
- 每个 case 单独记录状态
- 支持失败后继续下一个 case
- 支持恢复运行

---

## 阶段 6：领域建模（1 周）

### 学习目标
学会把自然语言任务转成结构化 schema / manifest。

### 要学内容
- manifest 设计
- schema 校验
- normalization
- quality assessment
- DSL / 配置设计

### 推荐资料
- JSON Schema 官方文档  
  https://json-schema.org/
- Pydantic 文档  
  https://docs.pydantic.dev/
- YAML 最佳实践资料
- 参考一些 CI/CD 配置、K8s YAML、OpenAPI 规范的设计思路

### 实战作业
定义一个 manifest schema，例如：

```yaml
version: 1
task_type: code_generation
repo_root: .
cases:
  - id: case_001
    target_file: src/foo.py
    goal: 为函数 foo 添加参数校验
    test_command: pytest tests/test_foo.py -q
    references:
      - docs/foo_design.md
```

并实现：
- schema 校验
- normalize 脚本
- quality assessor（至少检查必填项、路径存在、命令合法性）

---

## 阶段 7：IDE / Copilot / Claude / 企业插件适配（1 周）

### 学习目标
让 Agent 资产能给 IDE 插件识别和使用。

### 要学内容
- 各种工作区指令文件的约定
- Claude Code / Copilot / Continue / Cursor 等的配置方式
- 文件同步与软链接发布
- 宿主兼容层设计

### 推荐资料

#### GitHub Copilot
- GitHub Copilot Docs  
  https://docs.github.com/en/copilot

重点关注：
- 自定义 instructions
- workspace context
- prompt files

#### Claude
- Anthropic / Claude Code 官方文档  
  https://docs.anthropic.com/

#### Continue
- https://docs.continue.dev/

#### Cursor
- 官方文档 + 社区规则实践

#### MCP
- Model Context Protocol  
  https://modelcontextprotocol.io/

MCP 值得重点了解，因为未来工具接入会越来越标准化。

### 实战作业
做一个 `sync_agent_config.py`：
- 把 `agent_src/` 同步到：
  - `.claude/`
  - `.copilot/`
  - `.continue/`
  - `.cursor/`
- 使用 file-level symlink 或复制策略
- 支持打印发布清单

---

## 阶段 8：评测、可观测性、治理（1 周）

### 学习目标
让 Agent 不只是“能跑”，而是“能持续迭代”。

### 要学内容
- transcript 记录
- prompt / output / tool logs 记录
- benchmark
- 回归测试
- 失败分类
- 质量指标

### 推荐资料
- OpenAI Evals 相关资料
- Anthropic 关于 agent safety / eval 的文档
- promptfoo  
  https://www.promptfoo.dev/
- Humanloop / LangSmith（了解可观测性思想）

### 实战作业
给的 Agent 增加：
- 每次运行保存 prompt、stdout、stderr、结果
- 用 10 个固定任务做回归测试
- 统计成功率

---

# 四、按主题给列“重点学习资料清单”

下面是更聚焦的资料分类。

---

## A. LLM / Prompt / Tool Calling

### 必读
1. OpenAI Docs  
   https://platform.openai.com/docs

2. Anthropic Docs  
   https://docs.anthropic.com/

3. Prompt Engineering Guide  
   https://www.promptingguide.ai/

### 建议重点看
- system prompt
- structured output
- function/tool calling
- prompt templates
- long context handling

---

## B. Agent 框架与设计思路

### 推荐资料
1. LangGraph  
   https://langchain-ai.github.io/langgraph/

2. PydanticAI  
   https://ai.pydantic.dev/

3. OpenAI Agents SDK  
   官方文档搜索即可

4. AutoGen  
   https://github.com/microsoft/autogen

5. Semantic Kernel  
   https://github.com/microsoft/semantic-kernel

### 学习建议
不要一开始就深陷框架。  
先学思路：
- agent state
- tools
- handoff
- workflow
- retries

---

## C. 编码 Agent 开源项目

### 强烈建议重点研究
1. **Aider**  
   https://github.com/Aider-AI/aider

2. **Continue**  
   https://github.com/continuedev/continue

3. **OpenHands**  
   https://github.com/All-Hands-AI/OpenHands

4. **Cline**
5. **Roo Code**
6. **Cursor**（闭源，但可以研究其产品逻辑）

### 研究方法
不是只会安装使用，而是看：
- 目录结构
- prompt 组织
- tools 接法
- 文件编辑策略
- 上下文选择策略
- 日志与回放

---

## D. Workflow / 状态机 / 编排

### 推荐资料
1. LangGraph 文档
2. Temporal 文档  
   https://docs.temporal.io/
3. Prefect 文档  
   https://docs.prefect.io/
4. Airflow 文档（了解 DAG 思想）

### 重点
- checkpoint
- resume
- retries
- per-case isolation
- failure policy

---

## E. Python 工程化与 CLI

### 推荐资料
1. Python 官方教程
2. Real Python
3. Typer  
   https://typer.tiangolo.com/
4. Click  
   https://click.palletsprojects.com/

### 建议掌握
- CLI 开发
- 文件系统扫描
- 多进程/子进程
- pytest 集成
- logging

---

## F. YAML / Schema / 数据校验

### 推荐资料
1. Pydantic  
   https://docs.pydantic.dev/
2. JSON Schema  
   https://json-schema.org/
3. PyYAML

### 重点
- manifest schema
- validation
- normalization
- versioning

---

## G. IDE / Copilot / Claude / Continue / MCP

### 推荐资料
1. GitHub Copilot Docs  
   https://docs.github.com/en/copilot

2. Anthropic Docs / Claude Code  
   https://docs.anthropic.com/

3. Continue Docs  
   https://docs.continue.dev/

4. MCP  
   https://modelcontextprotocol.io/

### 重点
- workspace instructions
- prompt files
- 自定义 commands / tools
- context providers
- IDE 扩展接入方式

---

# 五、建议阅读顺序

如果怕资料太多，这里给一个**最优先阅读顺序**：

---

## 第一批（必须）
1. Python 官方教程 / Real Python
2. OpenAI Docs
3. Anthropic Docs
4. Prompt Engineering Guide
5. Aider 仓库
6. Continue Docs
7. Pydantic Docs
8. LangGraph Docs
9. MCP 官网
10. GitHub Copilot Docs

---

## 第二批（增强）
1. AutoGen
2. Semantic Kernel
3. OpenHands
4. Temporal
5. promptfoo / eval 资料

---

# 六、每周学习安排建议

---

## 第 1 周
### 学习
- Python CLI
- pathlib / subprocess / yaml / logging
- Git / symlink / submodule

### 输出
- 写一个命令行工具
- 能扫描目录、读写 markdown/yaml

---

## 第 2 周
### 学习
- OpenAI / Anthropic API
- prompt engineering
- structured output
- tool calling

### 输出
- 写一个 `llm_cli.py`
- 支持 system prompt + task prompt

---

## 第 3 周
### 学习
- context engineering
- 代码检索 / 文件筛选
- Aider / Continue 的上下文策略

### 输出
- 写 `context_builder.py`

---

## 第 4 周
### 学习
- 编码 agent 的 edit-test-fix 闭环
- transcript 记录

### 输出
- 写 `code_agent.py`
- 支持修改文件 + 跑 pytest + 修复 1 轮

---

## 第 5 周
### 学习
- rules / skills / agents 设计
- markdown 驱动配置

### 输出
- 目录结构升级：
  - `AGENTS.md`
  - `rules/`
  - `skills/`

---

## 第 6 周
### 学习
- workflow 设计
- 状态机基础
- checkpoint / result / resume

### 输出
- 写 `workflow_engine.py`

---

## 第 7 周
### 学习
- manifest schema
- pydantic / validation
- normalization

### 输出
- 写 `validate_manifest.py`
- 写 `normalize_manifest.py`

---

## 第 8 周
### 学习
- IDE 适配
- Copilot / Claude / Continue / Cursor 规则文件
- sync 脚本

### 输出
- 写 `sync_agent_config.py`

---

## 第 9~10 周
### 学习
- 多 case 运行
- per-case transcript
- failure policy

### 输出
- 写 `run_workflow.py`
- 支持 `--validate-only`
- 支持 `--max-cases`
- 支持 `--on-failure continue`

---

## 第 11~12 周
### 学习
- eval / benchmark / 可观测性
- prompt 迭代

### 输出
- 固定 10 个任务的 benchmark
- 记录成功率和失败原因

---

# 七、可以重点模仿的“项目结构模板”

学习时可以按这个结构练：

```text
my_coding_agent/
├── AGENTS.md
├── README.md
├── docs/
│   ├── architecture.md
│   ├── quickstart.md
│   └── design.md
├── agent_src/
│   ├── rules/
│   │   ├── coding-style.md
│   │   └── repo-conventions.md
│   ├── skills/
│   │   ├── context-builder.md
│   │   ├── code-writer.md
│   │   ├── reviewer.md
│   │   └── failure-analyzer.md
│   ├── agents/
│   │   ├── writer-agent.md
│   │   └── reviewer-agent.md
│   └── templates/
├── workflow/
│   └── code-generation/
│       ├── workflow.md
│       ├── workflow-partial.md
│       ├── manifests/
│       │   └── demo.yaml
│       └── tools/
│           ├── workflow_engine.py
│           ├── validate_manifest.py
│           ├── run_tests.py
│           ├── parse_logs.py
│           └── run_workflow.py
├── adapters/
│   ├── claude/
│   ├── copilot/
│   ├── continue/
│   └── cursor/
└── sync_agent_config.py
```

---

# 八、如果问“最该先学什么”，的排序是

按重要性从高到低：

1. **Python 工程化**
2. **Prompt / Tool Calling / Structured Output**
3. **Context Engineering**
4. **编码 Agent 闭环：edit -> test -> fix**
5. **rules / skills / workflow 设计**
6. **manifest / schema / validation**
7. **workflow engine / checkpoint / transcript**
8. **IDE / Copilot / Claude 适配**
9. **评测与治理**
10. **多 agent 高级架构**

注意：  
**不要一开始就冲 multi-agent。**  
先把单 agent + tools + workflow 跑通，价值最大。

---

# 九、给一份“精简必读清单”

如果只想先读最值得读的，先看这些：

### 基础
- Python 官方教程
- Pro Git

### 模型应用
- OpenAI Docs
- Anthropic Docs
- Prompt Engineering Guide

### 工程与 Agent
- Aider 仓库
- Continue Docs
- Pydantic Docs
- LangGraph Docs

### 集成
- GitHub Copilot Docs
- MCP 官网

---

# 十、给的实战建议：边学边做，不要纯看资料

最好的学习方式不是“读完再做”，而是每学一块就产出一个文件。

例如：

- 学 prompt → 写 `AGENTS.md`
- 学规则 → 写 `rules/coding-style.md`
- 学 tools → 写 `run_tests.py`
- 学 schema → 写 `manifest_schema.py`
- 学 workflow → 写 `workflow.md`
- 学 IDE 适配 → 写 `sync_agent_config.py`

这样 4~8 周之后，手里就已经有一个雏形仓库了。

---

如果愿意，下一条可以继续直接给：

### 1）一份“按天拆分”的 30 天学习计划表  
或者  
### 2）一份“每个主题对应的中文/英文资料清单”  
或者  
### 3）直接给一个 **编码 Agent 学习仓库模板**（目录+示例文件内容）

如果想要最实用的，建议下一条直接给：

> **“30天详细学习计划 + 每天做什么 + 每天看哪些资料”**
