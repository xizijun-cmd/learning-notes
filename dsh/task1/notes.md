# DSH · Task1 骨架笔记（导读 + 第 1 章）

> **核心一句话**：模型只生成 Token，Harness 把输出变成可执行、可验证、可纠错的行动——DSH 公开的是**组织方式**，不是具体内容。

## 一、为什么需要 Harness（0.1）

- 错觉：模型变强 + 接几个工具 = 好用的 Agent。
- `Agent = Model + Tools` 只适合最小原型；真实项目还要管：上下文、状态、权限、持久化、可观测性。
- **完整公式**（作者声明：不必当严格公式）：
  `Agent System = Model + Context + Tools + State + Loop + Execution Environment + Policy + Persistence + Observability`
  Harness 负责组织这些部分。
- **Harness 两项职责**：① 连接模型与外部世界；② 约束模型能做什么、怎样做、留下什么状态。
- 核心问题：不只"如何让模型更好地思考"，还有"如何让输出变成**可执行、可验证、可纠错**的行动"。

**10 问 ↔ 公式要素**（10 问 = 逐个点名要素）：

| 10 问 | 对应要素 |
|---|---|
| 看到什么上下文 | Context |
| 调用哪些工具 | Tools |
| 工具在哪执行 | Execution Environment |
| 哪些操作需授权 | Policy |
| 失败后如何继续 | Loop |
| 状态保存在哪 | State / Persistence |
| 一轮何时结束 | Loop |
| 能否派生子 Agent | Subagent（后续章节） |
| 能否运行代码 | Policy / Sandbox |
| 如何恢复审计调试 | Persistence / Observability |

> 一句话记忆：Agent Performance = f(Model, Harness, Tools, Context, Policy, Environment)。

## 二、为什么 DSH 值得单独写一本书（0.2 + 1.4）

- 前提：若只是又一个能改代码的 Agent，不值得写书。
- **理由**：它公开了一种不同的组织方式——Model Adapter、Tool Registry、Session Log、Agent Loop 等**产品能力都进插件体系**，扩展能力**不必修改特权产品核心**。
- **"一切皆插件" ≠ 没有核心**：Cordis 定义 Plugin / Context / Service / Event / Effect / Lifecycle 组合语义；核心负责规定组件**如何组合和退出**。
- 保守判断（1.4）：研究价值来自**公开的模块边界和扩展机制**，不是行业地位；叫"Agent 操作系统 / 微内核"还太早。

> 一句话记忆：值得写书的不是这个产品，是"组织方式"这个样本。

## 三、产品 vs Runtime（1.1）

| | 产品 | Runtime |
|---|---|---|
| 视角 | 用户界面：选 Workspace、输任务、批准敏感操作、拿结果 | 架构：组件组合的运行结果 |
| 本质 | 装配出来的一个组合 | "Agent 具备哪些能力"= 装配问题 |

- 例子：会话 A 要 FS / Shell / Skills / Plan / Subagent / Workflow / Sandbox / Approval；会话 B 只要 Bash + 编辑器 + 固定 Prompt。
- 传统框架：维护 3 套配置甚至 3 套 Loop；DSH：**4 套 Preset 按会话组合能力**。
- 4 套 Preset：Standard / PTC / Minimal / Creative（第 3 章拆解）。

> 一句话记忆：同一个 Runtime，按会话装配出不同产品。

## 四、一切皆插件（1.2）

- **误解**：想换就换、想删就删 → 并不是。组件有**依赖关系**，依赖的能力消失，组件无法运行或被卸载。
- 统一的是**组织方式**，不是组件职责。

| 组件 | 职责 |
|---|---|
| 模型适配器 | 连接不同大模型 |
| 工具运行时 | 注册、组织、执行工具 |
| 会话系统 | 保存状态与事件 |
| 智能体循环 | 推动推理、工具调用、下一步 |
| FS / 沙箱 / 子智能体 | 不同类型的执行能力 |

- 运行时统一管理：**加载、依赖、作用域、生命周期**。
- **依赖"能力"，不依赖"固定实现"**：要"文件系统"这种能力，谁提供由运行环境定（Local FS / Remote FS / Container FS），工具不关心底层。
- 类比：电源插座。

> 一句话记忆：上层只问"我要什么能力"，由谁实现运行时说了算。

## 五、不只是"多加几个 Tool"（1.3）

| | 普通 Tool Framework | DSH |
|---|---|---|
| 开放什么 | registerTool × N | Tool + Tool 周围的运行机制 + Tool 所在的 Runtime |
| Loop | 固定（call_model → execute_tool） | 插件可参与调用前检查、执行、结果处理、Loop 后续控制 |

- Extension Cookbook 扩展点：Tool 注册 / pre-execute 拦截 / 单调防护 / 执行包装器 / 后置处理 / UI 渲染 Session 事件 / follow-up·steer。
- 扩展经**明确的 Runtime 契约**接入，不改写默认 Loop。

> 一句话记忆：加 Tool 在工具圈做加法，一切皆插件把 Runtime 也开放。

## 六、Developer Preview（1.5）

- 技术冻结日 2026-08-24，版本固定到提交 `b150a551…`；用后文会分开标注"版本敏感事实"与"架构解释"。

| 稳定（架构语义） | 易变（具体实现） |
|---|---|
| Harness 职责、插件组合、Context/Service/Effect、Agent Loop、Tool Pipeline、Session Event、Capability/Provider、Sandbox/Approval/Lifecycle 边界 | CLI 参数、package 名、Preset 目录、UI、事件名、Code Mode API、Subagent backend、Extension API |

> 一句话记忆：骨架放心依赖，细节别当契约。

## 七、全书学习路径（0.3）

8 步：跑起来 → 4 套 Preset → 亲手加能力（Skill/Tool/Hook）→ Everything is a Plugin → Cordis → 追踪一次 Agent Turn → 高级能力（Code Mode/Subagent/Sandbox/Extension）→ 设计自己的 Harness。

主线：上手 → 组合 → 原理 → 运行时 → 设计。每章答 4 问：解决什么工程问题 / 官方实现放哪 / 运行时为什么这样工作 / 怎样实验核对。

## 速记

1. 模型只生成 Token；Harness 让输出可执行、可验证、可纠错
2. Agent System 公式 9 要素，Harness 负责组织；10 问 = 逐个点名要素
3. Harness 两项职责：连接 + 约束
4. 值得写书：产品能力全进插件体系，扩展不改特权核心
5. 一切皆插件 ≠ 没有核心；核心管"如何组合和退出"
6. 插件化：依赖能力不依赖实现；不是想删就删（有依赖关系）
7. 加 Tool 在工具圈做加法，一切皆插件把 Runtime 也开放
8. 架构语义稳定，具体实现易变

## ⚠️ 待确认

- **PTC** 含义原文未揭晓 → **已在 Task2 表 2-1 揭晓**：PTC = Standard + Code Mode 的工具呈现（官方目录 `code/`）。
- 10 问是全书要逐章回答的问题；第 1 章已能部分推断 ③（FS 由运行环境决定）和 ⑧（Subagent 是一种执行能力组件），其余待后续章节。
- 作业 B 已成稿，方向与 0.2 一致。

## 下一步（Task2 预告）

本章只讲了"Harness 负责组织"这个结论，**没讲组织起来长什么样**。带着三个问题进入 Task2：

1. 抽象的"组织方式"，落到真实的 Workspace / Preset / 模型接入上，具体是什么？
2. 4 套 Preset（Standard/PTC/Minimal/Creative）能力到底差在哪？**从 Trace 取证，别信模型自述**。
3. 为什么官方 Benchmark 选 Minimal？

→ 对应学习路径第 1–2 步「跑起来 → 4 套 Preset」。→ [进入 Task2](../task2/)
