# DSH 学习大汇总（Task1–4 · 导读 + 第 1–3 章）

> 这份笔记把四个 Task 的核心内容熔炼成一本合订本。需要逐字细节时，回到对应 Task 的 notes.md 查原文骨架。

## 〇、一句话总纲

**模型只会说，Harness 让它会做；Harness 拆两半（共享 vs 各自），Preset 是配置单，Creative 能改自己，Kernel 守住不散架的规则。**

---

## 一、核心矛盾与 Harness 是什么（Task1 · 导读 0.1–0.3）

### 核心矛盾

模型只能生成"下一段概率上合理的输出"，Agent 系统要让输出变成**可执行、可验证、可纠错的行动**。问题不只是"如何让模型更好地思考"，还有"如何让输出变成行动"。

- 链条：Model（生成 Token）→ Harness（组织与约束：连接 + 约束）→ External World（代码 / Shell / Web）
- Agent = Model + Harness
- Agent System = Model + Context + Tools + State + Loop + Execution Environment + Policy + Persistence + Observability
- Agent Performance = f（Model, Harness, Tools, Context, Policy, Environment）
- 导读 10 问 = 逐个点名九要素；已答 3 个（① 能看什么上下文→Workspace、③ 工具在哪执行→Workspace/Shell、⑧ 能否派生子 Agent→Subagent 是一种执行能力组件），其余 7 个待后续章节

### 三个易混概念

| 概念 | 一句话 | 角色 |
|---|---|---|
| Function Calling | 模型输出"我要调某函数"的结构化文字 | **只是文字** |
| MCP | 让外部系统接工具的协议 | 一种接法，只是 Harness 一环 |
| Harness | 读懂 → 执行 → 回传 → 决策下一轮 | **真正干活** |

### 为什么 DSH 值得单独写一本书

市面上已有大量 Coding Agent，如果 DSH 只是又一个能改代码的 Agent，就不值得写书。它值得，因为它公开展示了一种**不同的组织方式**：

- Model Adapter、Tool Registry、Session Log、Agent Loop 这些产品能力全部进入插件体系——扩展能力**不必修改特权产品核心**
- 但**不等于没有核心**：Cordis 定义了 Plugin / Context / Service / Event / Effect / Lifecycle 的组合语义，核心只管"如何组合和退出"
- 为什么自己写书：书里教的是**如何组织**，不是"把 DS 照抄一遍"

> 一句话记忆：**公开的是组织方式，不是具体内容。**

### 产品 vs Runtime

产品 = 有界面、默认配置、开箱即用（房子）；Runtime = 底层引擎（地基）。"一个 Agent 具备哪些能力"变成了**装配问题**。四套 Preset = 同一套 Harness 的预设配置（Standard 通用稳定 / PTC / Minimal 最小快速 / Creative 自由少约束）。

### "一切皆插件"的雏形（第 1 章 1.3–1.4）

| | 职责 | 组织方式 |
|---|---|---|
| 是什么 | 每个插件自己干什么 | 对外接口、被调度 / 拦截 / 循环控制 |
| 例子 | 模型适配器连模型、会话系统存状态 | 加载、依赖、作用域、生命周期 |
| 是否统一 | **不统一**，各管各的 | **被统一到一套规则下** |

- 误判：想换就换想删就删 → 实际有**依赖关系**，不是想删就删
- 判断标准：换插件，变的是职责，不变的是组织方式
- 关键：**依赖"能力"，不依赖"实现"**（要的是"文件系统"这种能力，谁提供由运行环境定：Local FS / Remote FS / Container FS）
- 类比：电源插座——统一"怎么插"，不管插上来的是台灯还是电风扇；**机制是 Harness 的，能力是插件的**

> 一句话记忆：上层只问"我要什么能力"，由谁实现运行时说了算。

### 为什么"不只是多加几个 Tool"

| | 普通 Tool Framework | DSH |
|---|---|---|
| 开放什么 | registerTool × N | Tool + Tool 周围的运行机制 + Tool 所在的 Runtime |
| Loop | 固定 | 插件可参与调用前检查、执行、结果处理、Loop 后续控制 |

Extension Cookbook 七个扩展点：Tool 注册 / pre-execute 拦截 / 单调防护 / 执行包装器 / 后置处理 / UI 渲染 Session 事件 / follow-up·steer。扩展经**明确的 Runtime 契约**接入，不改写默认 Loop。

### Developer Preview（1.5）

- 技术冻结日 2026-08-24；架构语义稳定（Harness 职责、插件组合、Context/Service/Effect、Agent Loop、Tool Pipeline、Session Event、Capability/Provider、Sandbox/Approval/Lifecycle 边界）
- 易变：CLI 参数、包名、Preset 目录、UI、事件名、Code Mode API、Subagent backend、Extension API

> 一句话记忆：**骨架放心依赖，细节别当契约。**

---

## 二、跑起来：Workspace 与 4 套 Preset（Task2 · 第 2 章）

### 环境与 Workspace

- Node ≥ 22.19 → `npx @deepseek-ai/dsh web` → 127.0.0.1:3080
- **Workspace = Agent 的"现实世界"**：决定它能看/改哪些文件、Shell 在哪个目录、Context 怎么建
- 安全习惯：只在空目录 dsh-lab 实验（我的实测：`/home/jie/project/DSH/dsh-lab/`，各 Preset 各占子目录）

### 模型是可替换能力

模型不是 DSH 本体，是 Provider / Model Adapter 挂载的能力。我接的是 MiniMax 2.7 highspeed（云端 API，openai 兼容），WSL 环境跑通。

### 两套实验（实测数据）

**实验 1 · 能力对照**（问"你当前能用哪些能力"）

| Preset | 工具/能力（Trace 自述） | 交叉验证 |
|---|---|---|
| Standard | bash、read、write、edit、glob、grep | 实验 2 实际用了 ✅ |
| PTC | 同一套工具，**标注"通过 run_code 调用"** | 直接调 write 报 UNKNOWN_TOOL ✅ |
| Minimal | 清单短且笼统（文件操作 + Bash） | **自述不可信的实证** ⚠️ |
| Creative | 文件操作 + bash + **web_search/web_fetch** + Skills | 未用到网络工具，待验证 |

- 关键发现：**Minimal 自述"能创建文件"，实际没有 write，用 echo 重定向**——模型自述会漏报/多报/模糊合并，**取证只看 Trace**

**实验 2 · 轨迹差分**（读目录 → 建 3 个文本文件 → 统计总行数 → 摘要）

| 观测项 | Standard | PTC | Minimal | Creative |
|---|---|---|---|---|
| 建文件方式 | write ×3 | 先失败 2 次→run_code{write×3}→run_code{read×3} | echo > 重定向 ×3 | write ×3 |
| 轮次 | 约 2 | **4** | 约 2 | 约 2 |
| 工具调用 | 5 | **12+** | 5 | 5 |
| 结果总行数 | 12 | 11 | 10 | 7 |

- 任务未规定文件内容，各模式行数不同是正常的——**比的是轨迹与方式，不是数值**
- PTC 的两张红色 UNKNOWN_TOOL 是"换呈现"的铁证：write 存在，直接调却不给入口
- PTC 轮次最多 + 自我纠正 → 减少 round trip 是**目标**，不是**性能保证**（任务太简单，写代码的准备比直接调还贵）

### 三个设计边界（2.4.2）

1. **能力存在 ≠ 能力如何呈现**（两件事；PTC 的 UNKNOWN_TOOL 是铁证）
2. **Preset ≠ System Prompt**：Preset 影响五个格子——Persona、Prompt Section、Tools、agent-local Service、部分 Runtime 行为；只在 Prompt 里写"你可以联网"调不出未挂载的工具
3. **Host 共享、Session 独立**：一个进程 4 个 Session 共享 Web UI / Model Route / Tool Registry / Persistence（机制共享）；各自有子目录、对话记录、Scope（内容独立）

### Benchmark 为什么选 Minimal（2.5）

| | Model Benchmark | Agent Benchmark |
|---|---|---|
| 测什么 | 纯模型能力 | 模型 + Runtime 的整体 |
| 变量 | 只有模型 | 模型、Prompt、Tool、Loop、上下文、策略 |

- 同一模型在 Standard 下表现更好，可能是 Harness 的功劳；复杂 Harness 引入多余 Tool Call，性能下降也不能归因模型
- 研究 Agent 除问"什么模型"，还要问"什么 Harness、什么工具/上下文/策略"
- 官方选 Minimal：砍掉 Harness 花样，结果逼近"测模型本身"

> 一句话记忆：Harness 越隐形，越知道模型几斤几两。

---

## 三、"一切皆插件"的含义（Task3 + Task4 · 第 3 章）

### 4 套 Preset ≠ 4 个 Agent

误解：4 个 Agent / 4 套代码 / 4 个不同 Loop。实际：Host 保留一套共享常驻组合 → Session 进入自己的 Agent 作用域 → 挂载对应 Preset 的 Tool / Prompt / 会话级能力。**Preset 定义的是"当前会话挂哪些能力"，不是一套独立 Agent。**

> 一句话记忆：一个进程，一套地基；三间房，三种装修。Preset 是装修单。

### 四个概念

| 概念 | 一句话 | 归属 / 边界 |
|---|---|---|
| Host Plane | 共享地基：Tool Registry、Persistence、Model Route、Sandbox/Approval、Subagent Registry | 共享、常驻 |
| Agent Plane | 会话组合：Persona、Prompt、Tool 注册与呈现、Agent-local service | 跟随 Agent / Preset |
| Scope | 能力"属于谁、谁能看见、跟谁一起销毁" | 边界 |
| Preset | 配置单：只决定当前会话挂哪些 Agent 级能力 | 不是 Agent、不是拷贝 |

- **分工原则，不是物理边界**：别问"这个工具属于哪层"，问"这件事属于哪层"。拿 write 来说：真正写盘（filesystem service + policy）在 Host；模型能否看见、叫什么名、参数怎么填在 Agent

### 两种极端与中间路线

| 方案 | 问题 |
|---|---|
| 全部放全局 | A 的新增会影响 B，互相干扰、同名冲突 |
| 每个 Agent 单独起一套基础设施 | 模型路由、持久化、沙箱、注册中心重复创建；贵、难统一 |
| **DSH 中间路线** | Host 持 process-singleton，Agent Scope 挂 Preset |

**作用域三问**（每挂一项能力都要答）：属于谁？谁能看见？跟随谁一起销毁？

### Standard = 能力基线

组合项：Persona/Instructions、Filesystem、Shell、Search/Web、Skills、Plan/Goal、Context/Compaction、Subagent、Workflow、Interaction。看点不是数量，是每项**属于哪个作用域、谁提供、向谁开放**。

表 3-1 四类组合项：

| 类别 | 负责什么 | 典型内容 | 生命周期 |
|---|---|---|---|
| 角色与提示词 | 定义身份、行为、提示内容 | Persona、Prompt Section、Skill Catalog | 跟随 Agent/Preset |
| 面向模型的工具 | 决定暴露哪些 Tool | FS/Shell/Web/Goal/Subagent | 跟随 Agent/Preset |
| 会话级状态 | 保存当前会话运行状态 | Plan/Goal/Workflow 会话级部分 | 跟随 Agent/Session |
| Host 能力适配项 | 把 Host 能力接进当前 Agent | Tool Registry、Model Route、Sandbox | Host 提供、Agent 消费 |

图 3-4：模型看到的每个工具，背后都站着一个 Host 服务——tool-fs→filesystem service+policy、tool-bash→shell executor、tool-jobs→jobs registry、tool-goal→goal service、tool-subagent→subagent registry、tool-web→web service/provider。

**设计自定义 Preset 的口诀**：先判断改哪一层，再决定改什么。
- "当前会话不许写文件" → Agent Plane 调工具集
- "所有文件操作必须进远程容器" → 换 Host Plane 的 FS/Sandbox Provider

### PTC = 同一批能力，不同呈现

- 目录 `code/`；不是重写 Agent，而是 Standard + Code Mode SDK + 改 Tool Presentation（`mode: code`）
- 区分两个问题：系统里有哪些工具（Tool Registry，Host，不变）vs 工具怎么给模型（Presentation，Agent，可变）
- Native：LLM→Tool A→LLM→Tool B→LLM→Tool C；PTC：LLM→生成 TS 程序（循环/条件/并行）→run_code→结果→LLM
- 关键：**能力拥有**与**使用方式**解绑
- Cordis 依赖：codeRuntimeService 不存在时，依赖它的组件停在 PENDING，不是等调用才报错
- 同一进程多 Session 共享同一个 Tool Registry，却各自用不同呈现方式

> 一句话记忆：不换工具，只换用法。

### Minimal = 观察基线

不是"弱版 Standard"，而是故意做薄：固定 Prompt + 持久 Bash + str_replace_editor；`includeRuntimeContext: false`、不启用上下文压缩——砍的不只是工具，还有提示词来源、运行时上下文、决策复杂度。

它是"Model + 极简 Harness"的**观察基线**（这也是 Benchmark 选它的原因）。但 Host 没撒手：终端会话仍受沙箱策略约束——**模型看不见 ≠ 底层不干活**。

### Creative = 递归：Agent 改自己的底盘

官方目录 `cordis/"，"创造"不是写作创意，而是 Agent 在授权范围内**观察和修改 Runtime 本身**。在 Standard 基础上增加：Runtime 检查能力、Cordis 相关工具、插件实验能力、Preset 编写指导。

`cordis_mount`：执行模型生成的 TypeScript，在当前运行的 Runtime 里挂载/卸载插件。七步（图 3-7）：Agent → 检查 Runtime → 生成临时 Plugin → Mount → 当前 Session 获得新能力 → 使用/验证 → Unmount。

- Self-modification：不是改模型参数，是**让 Runtime 的组合结构成为可操作对象**；运行一个 Agent 和设计一个 Agent 的边界变模糊
- 它改的是**自己作用域内**那份组合——改不到隔壁 Session，也改不到 Host 的仓库
- 风险边界：官方定义为**信任边界，不是安全沙箱**。能力越强，越需要外面的机制兜：权限、审查、版本、测试、恢复
- 所以别说成"安全的自我进化"；准确说法：**能改，但改得对不对、能不能恢复，取决于外部工程机制**

> 一句话记忆：能改自己那份，改不到隔壁，也改不到仓库。

### 4 套 Preset 综合对比（图 3-8）

| Preset | 定位 | 相对 Standard 改了什么 |
|---|---|---|
| Standard | 完整 Agent composition | 基线：文件编辑、Shell、检索、Skills、Planning、Subagent、Workflow 按需装配 |
| PTC | Standard + 换 Tool Presentation | 保留标准能力，把交互换成代码（TS 编排多步） |
| Minimal | 大幅裁剪 Prompt/Tools/Context | 精简 Persona/Context、常驻 Bash、str_replace_editor |
| Creative | Standard + 运行时自引用 | runtime inspection、model-written mount/unmount、Preset 编写指导 |

"插件化"不只针对 Tool：角色设定、提示词、工具呈现方式、上下文策略、部分 Runtime 能力，全都是可组合的部分。

### Cordis Kernel = 不散架的规则

Kernel 只管四条规则（**不属于任何 Preset**）：插件如何挂载和卸载、能力如何被发现和依赖、事件如何传递、资源退出时如何清理。

为什么"挂载"本身不能是插件：挂载管的是"插件怎么组合"这件事本身；如果"管组合"的规则也能被组合掉，就没有任何稳定的东西保证组合的正确性——**裁判不能由参赛队自己换**。

四条原因（讨论补充）：

| 职责 | 不稳定会怎样 |
|---|---|
| 挂载 = "谁能进来"的唯一入口 | 写错的插件混进来，该进的进不来 |
| 挂载管**依赖顺序** | A 依赖 B，B 没到 A 先挂上 → 拿到空壳（PENDING 就是这套机制在等） |
| 卸载管**资源清理** | 句柄、进程、连接没人回收 → 泄漏 |
| 事件传递 | 乱序/丢失 → 插件协作断裂 |

微内核类比：思路对（核心精简、功能外置），**类型不对**（DSH 不是 OS 微内核，没有进程/内存/设备管理，只是 Agent Runtime 的插件体系）。

> 一句话记忆：**插件是能换的零件，内核是换零件时"不散架"的规则——规则本身不能是零件。**

### 本章小结（3.9 原文 5 条）

1. Preset 决定当前 Session 组合哪些 Agent 能力——Agent 在自己的作用域挂载需要的部分，不复制一整套
2. Host Plane 负责共享与治理基础设施；Agent Plane 决定当前 Session 如何组合、配置、向模型呈现
3. PTC 与 Standard 的差异在工具的呈现和调用方式——底层 Tool Registry 不变
4. Minimal 通过减少提示词、运行时上下文、可见工具，把 Harness 压薄——观察"Model + 极简 Harness"的基础环境
5. Creative 把 Runtime 组合结构变成 Agent 可观察、可修改的对象——可扩展性提高，信任边界同时扩大，需要更严格的权限、审查、恢复机制

---

## 四、关键区分表（最容易混的六对）

| 易混 | 左边 | 右边 |
|---|---|---|
| 能力 | **存在**（在不在） | **呈现**（怎么给模型看） |
| 分层 | **Host Plane**（提供） | **Agent Plane**（组合呈现） |
| 证据 | **自述**（不可信，会漏/多/模糊） | **Trace**（客观，唯一取证） |
| 评测 | **Model Benchmark**（纯模型） | **Agent Benchmark**（模型 + Runtime） |
| 边界 | **信任边界**（能改，但要兜） | **安全沙箱**（出不去） |
| 插件化 | **多注册几个 Tool**（工具圈加法，Loop 固定） | **一切皆插件**（组织方式开放，呈现/拦截/结果处理/Loop 都可改） |

## 五、术语速查（英文 → 中文）

| 英文 | 中文 | 首次 |
|---|---|---|
| Harness | 挽具 / 框架 | 导读 |
| External World | 外部世界（代码/Shell/Web） | 导读 |
| Preset | 预设配置单 | 导读 |
| Workspace | 工作区 = Agent 的现实世界 | 第 2 章 |
| Provider / Model Adapter | 提供方 / 模型适配器 | 第 2 章 |
| Scope | 作用域 | 第 3 章 |
| Host Plane | 宿主层（共享） | 第 3 章 |
| Agent Plane | 会话层（各自） | 第 3 章 |
| Scope Context | 作用域上下文 | 第 3 章 |
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

## 六、我的实验证据清单（Task2 实测 · WSL · MiniMax 2.7 highspeed）

1. 4 个 Session 只启动一次 DSH → 共享证明
2. PTC 直接调 write 报 2 次 UNKNOWN_TOOL，改在 run_code 里调成功 → **存在 vs 呈现**的铁证
3. Minimal 没有 write，用 `echo >` 重定向 → 清单裁剪 + 自述不可信（"能创建文件"是假的）
4. 同一任务 4 套 Preset 结果数值不同（12/11/10/7 行）→ 比的是轨迹不是数值
5. PTC 4 轮 12+ 次调用，最重 → 减少往返是目标不是保证
6. Creative 上下文多出 Skills 声明（agent-experience）+ web_search/web_fetch

## 七、速记（全书前 3 章）

1. 核心矛盾：模型只生成 Token，Agent 要完成任务
2. Agent System 九要素，Harness 负责组织；10 问 = 逐个点名
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

## 八、自测问题汇总

**导读 10 问**（已答 3：① 上下文→Workspace、③ 执行位置→Workspace/Shell、⑧ 子 Agent→执行能力组件；余 7 待后续章节）

**Task2 三问**：① 自述为何不可信→是猜，会漏/多/模糊，看 Trace（Minimal 实证）② Preset 改存在还是呈现→两件事，PTC 的 write 存在但换入口 ③ 纯模型强是否任何 Runtime 都强→不一定，模型只是 f() 六变量之一

**Task3 三问**：① 分层避免哪两种极端→全放全局（互相干扰）/ 各起一套（重复建）② 会话互相干扰出在哪层→Scope 注册错了 ③ PTC 与 Standard 源码差异多小→只加 mode:code + Code Mode SDK

**Task4 四问**：① cordis_mount 做了什么→执行模型写的 TS，挂载/卸载插件 ② Kernel 管哪些不属于 Preset 的规则→四条 ③ 微内核类比哪里对哪里不对→思路对，类型不对 ④ 能否用 Task3/4 解释 Task2 差异→能，同一 Registry，Agent Plane 组合不同

## 九、待后续章节

- 导读 10 问剩余 7 个（历史状态保存、恢复审计调试等）
- Cordis 的 Context / Service / Event / Effect / Lifecycle 细节
- **第 4 章：亲手写 Skill / Tool / Hook**——把前四章的概念落到代码上

## 十、怎么用这份笔记复习

1. 先背**一句话总纲**，脱稿说出来
2. 看速记 16 条，卡住任何一条就回对应章节
3. **关键区分表**：遮住右边，看左边能说出"哪边是哪边"
4. 脱稿答 3 个自测问题（随机挑）
5. 看概念地图（task4/homework-a.html）把 17 个术语串起来
6. 需要细节回 task1–4 的 notes.md；实验数据回 task2/homework.md
