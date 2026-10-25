# DSH 完全掌握笔记（Task1–4 · 导读 + 第 1–3 章）

> 本文件目标：一份文件掌握全部学习内容。⭐ = 核心重点（必掌握），⚠️ = 难点 / 易错点（常考、易混）。
> 标记方式：`⭐` 必掌握；`⚠️` 难点易错；⭐⚠️ 既是重点又是难点。

## 一句话总纲

**模型只会说，Harness 让它会做；Harness 拆两半（共享 vs 各自），Preset 是配置单，Creative 能改自己，Kernel 守住不散架的规则。**

## 速记 16 条（先过一遍，后面展开）

1. 核心矛盾：模型只生成 Token，Agent 要完成任务
2. Agent System 九要素，Harness 负责组织；导读 10 问 = 逐个点名
3. 值得写书：产品能力全进插件体系，扩展不改特权核心；但不等于没有核心
4. 插件化：依赖"能力"不依赖"实现"；不是想删就删（有依赖关系）
5. 环境：Node ≥ 22.19 → npx → 3080；Workspace = Agent 的现实世界
6. Preset 改"能力如何呈现"，不改"能力是否存在"（两件事）
7. 模型自述会漏报/多报/模糊合并——取证只看 Trace
8. Model Benchmark ≠ Agent Benchmark；选 Minimal 是减少 Harness 变量
9. 4 套 Preset ≠ 4 个 Agent：一个 Host + 各自的 Agent Scope
10. Host 管"怎么有"，Agent 管"怎么用"——分工原则，不是物理边界
11. 作用域三问：属于谁 / 谁能看见 / 跟谁一起销毁
12. PTC = 同一批能力，不同呈现；减少往返是目标不是保证
13. Minimal = 观察基线：模型看见少，底层治理还在
14. Creative = 递归：改自己那份，改不到隔壁；信任边界不是沙箱
15. Kernel 四条规则：挂载卸载、能力发现与依赖、事件传递、资源清理
16. 规则本身不能是零件；微内核类比：思路对，类型不对

---

## 一、导读：核心矛盾与公式（Task1）

### 核心矛盾 ⭐

- 模型只能生成"下一段概率上合理的输出"，Agent 系统要让输出变成**可执行、可验证、可纠错的行动**
- 问题不只是"如何让模型更好地思考"，还有"如何让输出变成行动"
- 链路：Model（生成 Token）→ Harness（组织与约束：**连接 + 约束**）→ External World（代码 / Shell / Web）
- **Agent = Model + Harness**——不是"大模型 + 工具"那么简单

### Agent System 公式 ⭐⚠️

**Agent Performance = f（Model, Harness, Tools, Context, Policy, Environment）**

Agent System = Model + Context + Tools + State + Loop + Execution Environment + Policy + Persistence + Observability

- 模型：生成"下一个字"，提供推理与上下文生成能力
- 上下文：模型可读的输入，决定"知道什么"
- 工具：可供调用的能力，决定"能做什么"
- 状态：会话与执行状态，决定"记得什么"
- Loop：模型生成 → 工具调用 → 结果回传 → 模型生成下一步
- 执行环境：代码 / 命令 / 浏览器等实际执行方式
- 策略：权限、审批、范围、并发限制等治理边界
- 持久化：保存历史会话、执行记录、状态与审计日志
- 可观测性：日志、追踪、指标、复盘能力

### 导读 10 问（全书要逐章回答的问题）⭐

1. 模型能看什么上下文？
2. 模型能使用哪些工具？这些工具从哪里获得？
3. 这些工具在哪里执行？
4. 这个 Agent 是一次一问一答，还是可以在一个会话中连续对话？
5. 如果是连续对话，对话能记多久？
6. 中间的工具调用由谁管理？
7. 中途被打断了怎么办？
8. Agent 能不能派生自己的子 Agent？
9. 哪些边界由 Agent 自己控制，哪些边界需要外部约束？
10. 事后怎么复盘、审计和测试？

**已答 3 个**（其余待后续章节）：
- ① 能看什么上下文 → **Workspace**（第 2 章）
- ③ 工具在哪执行 → **Workspace / Shell 目录**（第 2 章）
- ⑧ 能否派生子 Agent → Subagent 是**一种执行能力组件**（第 1 章部分推断）

### 三个易混概念 ⚠️

| 概念 | 一句话 | 角色 |
|---|---|---|
| Function Calling | 模型输出"我要调某函数"的结构化文字 | **只是文字**——输出后不会自动执行 |
| MCP | 让外部系统接工具的协议 | 一种接法，**只是 Harness 一环** |
| Harness | 读懂 → 执行 → 回传 → 决策下一轮 | **真正干活** |

### 产品 vs Runtime

- 产品 = 有界面、默认配置、开箱即用（房子）
- Runtime = 底层引擎（地基）
- "一个 Agent 具备哪些能力"变成了**装配问题**
- 四套 Preset = 同一套 Harness 的预设配置：Standard（通用稳定）/ PTC / Minimal（最小快速）/ Creative（自由少约束）

---

## 二、第 1 章：为什么值得单独写一本书（Task1）

### 为什么值得写 ⭐

市面上已有大量 Coding Agent，如果 DSH 只是又一个能改代码的 Agent，就不值得写书。它值得，因为它公开展示了一种**不同的组织方式**：

- Model Adapter、Tool Registry、Session Log、Agent Loop 这些产品能力全部进入插件体系——扩展能力**不必修改特权产品核心**
- 但**不等于没有核心**：Cordis 定义了 Plugin / Context / Service / Event / Effect / Lifecycle 的组合语义，核心只管"如何组合和退出"
- 为什么自己写书：书里教的是**如何组织**，不是"把 DS 照抄一遍"

> 一句话记忆：**公开的是组织方式，不是具体内容。**

### "一切皆可插件"的职责 vs 组织方式 ⭐⚠️

| | 职责 | 组织方式 |
|---|---|---|
| 是什么 | 每个插件自己干什么 | 对外接口、被调度 / 拦截 / 循环控制 |
| 例子 | 模型适配器连模型、会话系统存状态 | 加载、依赖、作用域、生命周期 |
| 是否统一 | **不统一**，各管各的 | **被统一到一套规则下** |

- 误判：想换就换想删就删 → 实际有**依赖关系**，不是想删就删
- 判断标准：换插件，变的是职责，不变的是组织方式
- 关键：**依赖"能力"，不依赖"实现"**——要的是"文件系统"这种能力，谁提供由运行环境定（Local FS / Remote FS / Container FS），工具不关心底层
- 类比：**电源插座**——统一"怎么插"，不管插上来的是台灯还是电风扇

> 一句话记忆：上层只问"我要什么能力"，由谁实现运行时说了算。

### 为什么"不只是多加几个 Tool" ⭐

| | 普通 Tool Framework | DSH |
|---|---|---|
| 开放什么 | registerTool × N | Tool + Tool 周围的运行机制 + Tool 所在的 Runtime |
| Loop | 固定（call_model → execute_tool） | 插件可参与调用前检查、执行、结果处理、Loop 后续控制 |

Extension Cookbook **7 个扩展点**：① Tool 注册 ② pre-execute 拦截 ③ 单调防护 ④ 执行包装器 ⑤ 后置处理 ⑥ UI 渲染 Session 事件 ⑦ follow-up·steer

- 扩展经**明确的 Runtime 契约**接入，不改写默认 Loop
- "加 Tool"在工具圈做加法，"一切皆插件"把 Runtime 也开放

### Developer Preview（1.5）

- 技术冻结日 2026-08-24，版本固定到提交 b150a551…
- **稳定（骨架放心依赖）**：Harness 职责、插件组合、Context/Service/Effect、Agent Loop、Tool Pipeline、Session Event、Capability/Provider、Sandbox/Approval/Lifecycle 边界
- **易变（细节别当契约）**：CLI 参数、包名、Preset 目录、UI、事件名、Code Mode API、Subagent backend、Extension API

---

## 三、第 2 章：从零开始运行 DSH（Task2）

### 环境与 Workspace ⭐

- Node ≥ 22.19（`^22.19.0 || >=24`）→ `npx @deepseek-ai/dsh web` → 127.0.0.1:3080
- **Workspace = Agent 的"现实世界"**：决定它能看/改哪些文件、Shell 在哪个目录、Context 怎么建（答导读 10 问的 ①③）
- 安全习惯：只在空目录 dsh-lab 实验，报错先截图再求助
- 我的实测：WSL（Ubuntu）· Node v22.23.3 · workspace `/home/jie/project/DSH/dsh-lab/`（各 Preset 各占子目录）

### 模型是可替换能力

- 模型不是 DSH 本体，是 Provider / Model Adapter 挂载的能力
- 我接的是 MiniMax 2.7 highspeed（云端 API）；书中演示 vllm 本地 Qwen + openai-responses 协议
- 卡点对策：Node 版本不满足 → nvm；npx 拉包慢 → 镜像源或源码运行；本地跑不动大模型 → 云端 API

### 实验 1 · 4 套 Preset 能力对照（实测）⭐

> 方法：4 个 Session 问"你当前可以使用哪些能力"，**从 UI/Trace 取证，不是问模型它自己怎么说**。

| Preset | 暴露的工具 / 能力（Trace 自述） | Prompt / 上下文差异 | 取证与交叉验证 |
|---|---|---|---|
| Standard | bash（cd 需 workdir）、read / write / edit、glob、grep | runtime-context（workspace-write 沙箱策略） | 实验 2 实际用了 bash + write ✅ |
| PTC | **同一套工具，标注"通过 run_code 调用"** | Code Mode 约定 | 直接调 write 报 UNKNOWN_TOOL，须经 run_code ✅ |
| Minimal | 清单明显更短、更笼统：文件操作（读取/编辑/创建）+ Bash | 精简 | **自述含糊地说"创建文件"，实际没有 write、用 echo 重定向——自述不可信的实证 ⚠️** |
| Creative | 文件操作 + bash + **web_search / web_fetch** | runtime-context + system-reminder 声明 Skills（agent-experience） | 实验 2 未用到网络工具，自述待验证 |

- 结论：差异不在"有没有能力"，而在**暴露了哪些工具、以什么方式呈现**；模型自述与 Trace 对照后才发现 **Minimal 的自述最不可信**

### 实验 2 · 同一任务 4 种模式的轨迹差分（实测）⭐⚠️

> 任务：读目录 → 建 3 个文本文件 → 统计总行数 → 摘要。任务未规定文件内容，各模式行数不同（12/11/10/7），比的是**轨迹与方式**，不是数值。

| 观测项 | Standard | PTC | Minimal | Creative |
|---|---|---|---|---|
| 工具 / 能力 | bash + write | **只有 run_code**（write/read 藏在代码内部） | **只有 bash**（`echo >` 重定向） | bash + write，额外暴露 Skills |
| 角色设定 / Prompt | 系统提示 + runtime-context | 同左 + Code Mode 约定 | 精简 | runtime-context + Skills 声明 |
| 计划 / 步骤 | ls → write×3 → wc -l | 直接调 write **失败 ×2（UNKNOWN_TOOL）** → 自我纠正 → run_code{write×3} → run_code{read×3 计数} | ls → echo×3 → wc -l | ls → write×3 → wc -l |
| 轮次 | 约 2 | **4（含失败与自我纠正）** | 约 2 | 约 2 |
| 工具调用次数 | 5 | **12+** | 5 | 5 |
| 自主程度 / 粒度 | 粒度 = 单工具调用 | 粒度 = **一段代码**，自行编排子工具；具备自我纠错 | 最粗：shell 一把梭 | 粒度 = 单工具，内容发挥更自由 |
| 结果 | 3 文件共 12 行 | 3 文件共 11 行 | 3 文件共 10 行 | 3 文件共 7 行 |

- **PTC 的两张红色 UNKNOWN_TOOL 是全章最有价值的证据**——write 存在（run_code 里能调），但直接调报错：**存在没变，呈现变了**
- PTC 轮次最多、自我纠正 → **减少 round trip 是设计目标，不是性能保证**（任务太简单，写代码的准备工作比直接调还贵）

### 三个设计边界（2.4.2）⭐⚠️

**① 能力存在 ≠ 能力如何呈现**
- PTC 没有重新实现一套工具，只在已有能力上改变 model-facing presentation（面向模型的呈现状态）
- Harness 既决定模型可以用什么，也决定模型以什么形式看到这些能力

**② Preset 不只是 System Prompt**
- Preset 同时影响：**Persona、Prompt Section、Tools、agent-local Service、部分 Runtime 行为**——五个格子
- 切 Preset 是换**一整包配置**，不是改一段话
- 证据：Creative 的 web_search 是挂载的工具，在 Standard 的 Prompt 里写"你可以联网"调不出来——**Prompt 只是文字，改不了挂载**

**③ 同一 Host 下不同 Session 共享基础设施 + 各自 Agent Scope**
- Host：共享 Tool Registry / Model Route / Persistence / Sandbox（机制共享，全进程一份）
- Agent Scope：各自的 Prompt / 工具暴露 / 对话记录（内容独立，A 的改动 B 看不见）
- 精确区分：**机制共享，内容独立**——Persistence 的"管道"共享，但管道里流的每个 Session 的数据互相隔离
- 不是"每个 Session 复制一套完整插件树"：1 栋楼 + 4 个房间，不是 4 栋楼

### Benchmark 为什么选 Minimal（2.5）⭐

| 维度 | Model Benchmark | Agent Benchmark |
|---|---|---|
| 测什么 | 纯模型能力（答题、推理分） | 模型接入具体 Harness 之后的整体 |
| 变量 | 只有模型 | 模型 + Prompt + Tool + Loop + 上下文 + 执行策略 |

- 三个反向判断：① Standard 下表现更好可能是 **Harness 的功劳**，别归给模型 ② 复杂 Harness 引入多余 Tool Call / 更长执行链，**性能下降也不能归因模型**（PTC 4 轮 12+ 次就是例子）③ 研究 Agent 除问"什么模型"，还要问"什么 Harness、什么工具/上下文/策略"
- 官方选 **Minimal**：砍掉 Harness 花样，结果逼近"测模型本身"
- 4 套 Preset 把这些容易被忽略的 Harness 变量**显式暴露**出来

> 一句话记忆：Harness 越隐形，越知道模型几斤几两。

---

## 四、第 3 章：一切皆插件（Task3 + Task4 · 3.1–3.9）

### 4 套 Preset ≠ 4 个 Agent ⭐

- 误解：4 个 Agent / 4 套代码 / 4 个不同 Loop
- 实际（agent.cordis.yml）：Host 保留**一套共享常驻组合**（模型、注册机制、持久化）→ Session 进入自己的 Agent 作用域 → 挂载对应 Preset 提供的 Tool / Prompt / 会话级能力
- 图 3-2 的分层来自 **Creative Preset 的源码注释**——官方自己这么划分
- Preset 定义的是"**当前会话需要挂载哪些 Agent 级能力**"，不是一套独立 Agent

> 一句话记忆：一个进程，一套地基；三间房，三种装修。Preset 是装修单。

### 四个概念 ⭐⚠️

| 概念 | 一句话 | 归属 / 边界 |
|---|---|---|
| Host Plane | 共享地基：Tool Registry、Persistence、Model Route、Sandbox/Approval、Subagent Registry、shared infrastructure | 共享、常驻 |
| Agent Plane | 会话组合：Persona、Prompt Sections、Tool registrations/presentation、Agent-local services、Preset-specific behavior | 跟随 Agent / Preset |
| Scope | 能力"**属于谁、谁能看见、跟谁一起销毁**" | 边界 |
| Preset | 配置单：只决定当前会话挂载哪些 Agent 级能力 | 不是 Agent、不是拷贝 |

- **分工原则，不是绝对物理边界**——别问"这个工具属于哪层"，问"这件事属于哪层"
- 同一个 Tool 拆开看：实现/注册/执行设施在 Host；是否可见、叫什么名、参数格式、Native 还是 Code Mode 在 Agent

> 一句话记忆：Host 管"怎么有"，Agent 管"怎么用"。

### 两种极端与中间路线 ⭐

| 方案 | 问题 / 代价 |
|---|---|
| 全部放全局 | Session A 新增 Tool/角色/服务影响 Session B，互相干扰，甚至同名冲突 |
| 每个 Agent 单独起一整套基础设施 | 模型路由、持久化、沙箱、子 Agent 注册中心重复创建；成本高、难统一管理 |
| **DSH 中间路线** | Host 持 process-singleton 能力，各 Agent Scope 挂不同 Preset |

- Preset 能力只在当前 Agent 的 **Scope Context**（作用域上下文）中生效；Host 共享设施可被多 Session 共用
- 一项能力能被哪些 Session 看见，取决于**注册在哪个作用域**，不能只看文件/目录位置

### 作用域三问 ⭐

1. 这项能力**属于谁**？
2. **谁可以看到和使用**它？
3. 它应该**跟随谁的生命周期**一起创建和销毁？

- 注册错位置的后果：只该当前会话的服务注册到全局 → 所有 Session 可访问，甚至同名冲突

### Standard = 能力基线

- 组合项：Persona/Instructions、Filesystem、Shell、Search/Web、Skills、Plan/Goal、Context/Compaction、Subagent、Workflow、Interaction
- 看点不是数量，是每项**属于哪个作用域、由谁提供、向谁开放**

表 3-1 四类组合项：

| 类别 | 负责什么 | 典型内容 | 生命周期 |
|---|---|---|---|
| 角色与提示词 | 定义 Agent 身份、行为方式、提示内容 | Persona、Prompt Section、Skill Catalog | 跟随 Agent / Preset |
| 面向模型的工具 | 决定当前 Agent 暴露哪些 Tool | FS / Shell / Web / Goal / Subagent 等 Tool contribution | 跟随 Agent / Preset |
| 会话级状态 | 保存当前 Agent / Session 运行状态 | Plan / Goal / Workflow 的会话级部分 | 跟随 Agent / Session |
| Host 能力适配项 | 把 Host 已有能力接入当前 Agent | Tool Registry、Model Route、Sandbox、Subagent Registry | Host 提供、Agent 消费 |

> 注：任务说明把组合项归纳为**三类**（面向模型的工具 / 会话级状态 / Host 能力适配项），把"角色与提示词"并入；本书原表为四类，以书为准。

图 3-4：Preset 的 model-facing 工具，背后都依赖 Host 的 Registry / Provider：

| Preset 侧（模型可见） | Host 侧（真正执行） |
|---|---|
| tool-bash | shell / subprocess executor |
| tool-fs | filesystem service + policy |
| tool-jobs | jobs registry |
| tool-goal | goal service |
| tool-subagent | subagent registry / providers |
| tool-web | web service / provider |

**设计自定义 Preset 的口诀**：先判断要改哪一层，再决定改什么。
- "当前会话不允许用写文件工具" → Agent Plane 调工具集 / 使用限制
- "所有文件操作必须在远程容器执行" → 换 Host Plane 的 FS / Sandbox Provider

**为什么很多自定义 Agent 越做越复杂**：一开始没把两类问题分开——"模型能看到和使用什么能力"（Agent）与"这些能力在底层由谁在哪里执行"（Host）。混在一起，在错误的层上改东西，越改越乱。

### PTC = 同一批能力，不同呈现 ⭐

- 目录 `code/`，产品名 PTC；**不是重写 Agent**，而是 Standard + Code Mode SDK + 改 Tool Presentation（`mode: code`）
- 区分两个问题：**系统里有哪些工具**（Tool Registry，在 Host，不变）与**工具以什么方式提供给模型**（Presentation，在 Agent，可切）

| | Native Tool Calling | PTC（Code Presentation） |
|---|---|---|
| 模型看到 | JSON Tool Schemas | Typed TypeScript SDK |
| 流程 | LLM → Tool A → LLM → Tool B → LLM → Tool C | LLM → 生成 TS 程序（循环/条件/并行）→ run_code → 结果 → LLM |
| 特点 | 每步都回 LLM 决策 | 部分确定性流程交给代码，减少 round trip |

- 关键：**能力拥有**与**使用方式**解绑
- 取舍：减少 round trip 是设计目标，**不是无条件的性能保证**——取决于任务是否适合程序化编排、生成代码质量、返回数据量
- **同一进程多 Session 共享同一个 Tool Registry，却各自采用不同的工具呈现方式**——分层的直接体现
- Cordis 依赖：codeRuntimeService 不存在时，依赖组件停在 **PENDING**，等所需服务出现（不是等模型调 run_code 才报错）
- 注：DSH 更新快，具体依赖与缺依赖状态以当前版本实际声明为准（呼应第 1 章"骨架放心依赖，细节别当契约"）

> 一句话记忆：不换工具，只换用法。

### Minimal = 把 Harness 压到非常薄 ⭐

- 不是"弱版 Standard"，而是**尽量减少模型能直接看到和使用的 Harness 能力**
- 当前组合：`includeRuntimeContext: false`、不启用上下文压缩；按任务说明，特征是**固定 Prompt + 持久 Bash + str_replace_editor**
- 减少的不只是工具，还有提示词来源、运行时上下文、决策复杂度
- 定位：**"Model + 极简 Harness"的观察基线** → 接上第 2 章 Benchmark 为什么选 Minimal
- 但模型看不见 ≠ Host 不管：终端会话仍依赖 Host 的沙箱策略与命令执行环境

> 一句话记忆：模型看到的能力可以很少，底层执行边界和安全治理仍然存在。

### Creative = 递归：Agent 改自己的底盘 ⭐⚠️

- 官方目录 `cordis/"`，"创造"不是写作创意，而是 Agent 在授权范围内**观察和修改 Runtime 本身**
- 在 Standard 基础上增加：① 对 Runtime 的检查能力 ② Cordis 相关工具 ③ 插件实验能力 ④ 创建/修改 Preset 所需的指导

`cordis_mount` 做了什么（图 3-7）：

```
Agent → 检查当前 Runtime → 生成临时 Plugin（模型写 TypeScript）
     → Mount → 当前 Session 获得新能力 → 使用/验证 → Unmount
```

- 能**执行模型生成的 TypeScript 代码**，在当前运行的 Runtime 中挂载/卸载插件
- Agent 不只操作文件、脚本、网络——还能改变**承载自己的那套运行时组合**
- Self-modification 的准确含义：**不是改模型参数或权重**，是让 Runtime 的**组合结构**成为 Agent 可操作的对象
- 副作用："运行一个 Agent"和"设计一个 Agent"的边界变模糊

**递归的准确含义（讨论确认）**：

- 能改的，是**自己作用域内**那份组合；改不到隔壁 Session，也改不到 Host 的仓库（Tool Registry）
- 新挂的工具是进"这个 Session 的领料单"，不是进"公司仓库"
- 能改自己那份 Prompt（提示词属于 Agent Plane 的作用域）

**风险边界（最容易漏的一点）⚠️**：

- 官方把自修改定义为**信任边界（trust boundary），不是安全沙箱**
- 能力越强，越需要外层机制兜：权限控制、人工审查、版本管理、测试、恢复
- 别说成"安全的自我进化机制"。准确说法：**它能改，但改得对不对、能不能恢复，取决于外面的工程机制**
- 正因为改的是自己，出问题时**没有上一层兜底**

> 一句话记忆：Creative 给的是"改自己底盘"的能力，不是"改了不会翻车"的保证。

### 4 套 Preset 综合（图 3-8）⭐

- 一个 Agent 的最终表现，不是由某个固定 Agent 类型单独决定，而是三样东西共同形成：① Host 共享能力 ② Runtime 基础组合 ③ 当前会话通过 Preset 挂载的能力

| Preset | 一句话定位 | 相对 Standard 改了什么 |
|---|---|---|
| **Standard** | 完整 Agent composition | 基线：文件编辑、Shell、检索、Skills、Planning、Subagent、Workflow，按需装配到当前 Session |
| **PTC** | Standard + 换 Tool Presentation | 保留标准能力，把"模型 ↔ 工具"的交互换成代码（TS 编排多步） |
| **Minimal** | 大幅裁剪 Prompt / Tools / Context | 只留精简 Persona/Context、常驻 Bash、str_replace_editor，大幅缩小标准 Harness 能力 |
| **Creative** | Standard + 运行时自引用 | 加 runtime inspection、model-written mount/unmount、Preset 编写指导 |

- **"插件化"不只针对 Tool**：角色设定、提示词、工具呈现方式、上下文策略、部分 Runtime 能力，全都是可组合的部分

### Cordis Kernel = 不散架的规则 ⭐⚠️

Kernel 只管四条规则（**不属于任何 Preset**）：

1. 插件如何**挂载和卸载**
2. 能力如何**被发现和依赖**
3. **事件**如何传递
4. 资源在插件退出时如何**清理**

为什么必须有它：这些是 Runtime 的**基础语义**。只有它们稳定，插件才能被组合、替换、启停、销毁，而不会让整个系统失去一致性。

更准确的理解：**让 Kernel 只保留稳定的运行机制，把具体 Agent 能力交给插件和 Preset 去组合。**

**为什么"挂载"本身不能是插件（讨论补充）**：

| 职责 | 不稳定会怎样 |
|---|---|
| 挂载 = "谁能进来"的唯一入口 | 写错的插件混进来，该进的进不来 |
| 挂载还管**依赖顺序** | A 依赖 B，B 没到 A 先挂上 → 拿到空壳（PENDING 就是这套机制在等） |
| 卸载负责**资源清理** | 文件句柄、进程、连接没人回收 → 泄漏 |
| 事件传递 | 乱序/丢失 → 插件协作断裂 |

**本质是悖论**：挂载管的是"插件怎么组合"这件事本身；如果"管组合"的规则也能被组合掉，就没有任何稳定的东西保证组合的正确性。

- 类比一：**裁判不能由参赛队自己换**
- 类比二：**量长度的尺子不能是随时会缩的**

**微内核类比**：思路对（核心精简、功能外置），**类型不对**（DSH 不是 OS 微内核——没有进程/内存/设备管理，只是 Agent Runtime 的插件体系）。

> 一句话记忆：插件是能换的零件，内核是换零件时"不散架"的规则——**规则本身不能是零件**。

### 本章小结（3.9 原文 5 条）

1. Preset 决定当前 Session 组合哪些 Agent 能力——不是每个 Session 复制一整套插件和底层设施，Agent 在自己的作用域挂载需要的部分
2. Host Plane 负责共享与治理基础设施，Agent Plane 决定当前 Session 如何组合、配置、向模型呈现
3. PTC 与 Standard 的差异在工具的呈现和调用方式——底层 Tool Registry 不变，模型通过 Code Mode 用同一套能力
4. Minimal 通过减少提示词、运行时上下文、可见工具，把 Harness 压薄——它是观察"Model + 极简 Harness"的基础环境
5. Creative 把 Runtime 组合结构变成 Agent 可观察、可修改的对象——可扩展性提高，信任边界同时扩大，需要更严格的权限、审查、恢复机制

---

## 五、关键区分表（最容易混的六对）⭐⚠️

| 易混 | 左边 | 右边 |
|---|---|---|
| 能力 | **存在**（在不在） | **呈现**（怎么给模型看） |
| 分层 | **Host Plane**（提供） | **Agent Plane**（组合呈现） |
| 证据 | **自述**（不可信，会漏/多/模糊） | **Trace**（客观，唯一取证） |
| 评测 | **Model Benchmark**（纯模型） | **Agent Benchmark**（模型 + Runtime） |
| 边界 | **信任边界**（能改，但要兜） | **安全沙箱**（出不去） |
| 插件化 | **多注册几个 Tool**（工具圈加法，Loop 固定） | **一切皆插件**（组织方式开放，呈现/拦截/结果处理/Loop 都可改） |

## 六、术语速查（英文 → 中文 → 首次出现）

| 英文 | 中文 | 首次 |
|---|---|---|
| Harness | 挽具 / 框架 | 导读 |
| External World | 外部世界（代码/Shell/Web） | 导读 |
| Preset | 预设配置单 | 导读 |
| Workspace | 工作区 = Agent 的现实世界 | 第 2 章 |
| Provider / Model Adapter | 提供方 / 模型适配器 | 第 2 章 |
| Scope | 作用域 | 第 3 章 |
| Scope Context | 作用域上下文 | 第 3 章 |
| Host Plane | 宿主层（共享） | 第 3 章 |
| Agent Plane | 会话层（各自） | 第 3 章 |
| Composition | 能力组合 | 第 3 章 |
| Tool Registry | 工具注册表 | 第 3 章 |
| Tool Presentation | 工具呈现方式 | 第 3 章 |
| Model Route | 模型路由 | 第 3 章 |
| Persistence | 持久化 | 第 3 章 |
| Sandbox / Approval | 沙箱与审批 | 第 2 章 |
| Session | 一次会话实例 | 第 2 章 |
| Trace | 客观调用记录 | 第 2 章 |
| Self-modification | 自修改 | 第 3 章 3.6 |
| cordis_mount | 挂载工具 | 第 3 章 3.6 |
| Trust boundary | 信任边界 | 第 3 章 3.6 |
| Cordis Kernel | 内核 | 第 3 章 3.8 |
| process-singleton | 全进程唯一 | 第 3 章 |
| PENDING | 依赖未满足时的等待状态 | 第 3 章 |

## 七、我的实验证据清单（Task2 实测 · WSL · MiniMax 2.7 highspeed）

1. **4 个 Session 只启动一次 DSH** → Host 共享证明
2. **PTC 直接调 write 报 2 次 UNKNOWN_TOOL**，改在 run_code 里调成功 → **存在 vs 呈现**的铁证
3. **Minimal 没有 write，用 `echo >` 重定向** → 清单裁剪 + 自述不可信（"能创建文件"是假的）
4. **同一任务 4 套 Preset 结果数值不同（12/11/10/7 行）** → 比的是轨迹不是数值
5. **PTC 4 轮 12+ 次调用，是最重的一套** → 减少往返是目标不是保证
6. **Creative 上下文多出 Skills 声明（agent-experience）+ web_search / web_fetch**

## 八、自测问题汇总（完整问题 + 答案要点）

**导读 10 问**：① 能看什么上下文 ② 工具从哪拿 ③ 工具在哪执行 ④ 一次一问一答还是连续 ⑤ 对话能记多久 ⑥ 工具调用谁管理 ⑦ 中途打断怎么办 ⑧ 能否派生子 Agent ⑨ 边界谁控 ⑩ 怎么复盘审计测试。已答 3：①③→Workspace，⑧→Subagent 是执行能力组件；余 7 待后续章节。

**Task2 三问**：
- ① 自述为何不可信 → 自述是"猜"，会漏报/多报/模糊合并；看 Trace（Minimal 实证）
- ② Preset 改存在还是呈现 → **两件事**；PTC 的 write 存在但换入口
- ③ 纯模型强是否任何 Runtime 都强 → 不一定，模型只是 f() 六变量之一

**Task3 三问**：
- ① 分层避免哪两种极端 → 全放全局（互相干扰）/ 各起一套（重复建）
- ② 会话互相干扰出在哪层 → **Scope**（该注册在 A 的 Scope，却注册到全局）
- ③ PTC 与 Standard 源码差异多小 → 只加 `mode: code` + Code Mode SDK；改的是呈现，Registry 不变

**Task4 四问**：
- ① cordis_mount 做了什么 → 执行模型写的 TS，挂载/卸载插件；Runtime 成为可操作对象
- ② Kernel 管哪些不属于 Preset 的规则 → 四条：挂载卸载、能力发现与依赖、事件传递、资源清理
- ③ 微内核类比哪里对哪里不对 → 思路对（核心精简功能外置），类型不对（不是 OS 微内核）
- ④ 能否用 Task3/4 解释 Task2 差异 → 能：同一 Tool Registry（Host），四套 Preset 在 Agent Plane 的组合不同

## 九、重点难点清单（复习时优先过）

**⭐ 核心重点**
- Agent = Model + Harness；Harness 两项职责（连接 + 约束）
- 九要素公式与导读 10 问
- 值得写书：产品能力进插件体系，不等于没有核心
- Workspace = Agent 的现实世界
- Preset 改呈现不改存在
- 取证只看 Trace
- 4 套 Preset ≠ 4 个 Agent（一个 Host + 各自 Scope）
- Host 管"怎么有"，Agent 管"怎么用"
- 作用域三问
- Creative：cordis_mount 与递归
- Kernel 四条规则
- 一切皆可插件 vs 没有内核

**⚠️ 难点易错**
- Function Calling 只是文字（不是执行）
- 自述不可信（Minimal 实证）
- 存在 vs 呈现（UNKNOWN_TOOL 铁证）
- Preset ≠ System Prompt（五个格子）
- 机制共享 vs 内容独立（Persistence 的管道共享、数据各自）
- 死循环 vs 没有 Harness（"喊停坏了" vs "没人干活"）
- 信任边界 vs 安全沙箱
- 规则本身不能是零件（裁判不能自己换）
- 减少往返是目标不是保证（PTC 反例）

## 十、待后续章节

- 导读 10 问剩余 7 个（历史状态保存、恢复审计调试等）
- Cordis 的 Context / Service / Event / Effect / Lifecycle 细节
- **第 4 章：亲手写 Skill / Tool / Hook**——把前四章的概念落到一行行代码上

## 十一、怎么用这份笔记复习

1. 先背**一句话总纲**，脱稿说出来
2. 看**速记 16 条**，卡住任何一条回对应章节
3. **九块核心知识**：遮住内容，看标题能用自己的话讲一遍
4. **关键区分表**：遮住右边，看左边能说出"哪边是哪边"
5. **重点难点清单**：⭐ 过一遍，⚠️ 逐个自查能否解释"为什么"
6. 脱稿答 3 个自测问题（随机挑）
7. 需要实验数据看"我的实验证据"；需要关系图看 task4/homework-a.html（17 术语概念地图）；需要过程记录看 task4 notes 的五站问答
