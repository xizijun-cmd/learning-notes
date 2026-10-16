# DSH · Task2 骨架笔记（第 2 章：从零开始运行 DSH）

> **承接 Task1**：Task1 结论是"Harness 负责组织、开放的是组织方式"——但没说组织起来具体长什么样。本章把这个抽象概念落地成三样可摸的东西：**Workspace（现实世界）→ Preset（组合方式）→ 模型接入（可替换能力）**，并用实验验证。

> **核心一句话**：Task2 是"动手"——把 DSH 跑起来，用 UI/Trace 而非模型自述，验证 Preset 到底改了什么。

## 一、环境搭建（2.1）

- 要求：Node ≥ 22.19（`^22.19.0 || >=24`）
- 启动：`npx @deepseek-ai/dsh web` → 访问 `127.0.0.1:3080`
- 卡点：npx 拉包慢 → 换 npm 镜像源，或克隆仓库源码运行

## 二、Workspace = Agent 的"现实世界"（2.2）

- **定位**：决定 Agent 能看到/修改哪些文件、Shell 在哪个目录、Context 怎么建
- **安全习惯**：只在空目录 `dsh-lab` 实验，报错先截图再求助
- 对应 10 问中的 ①（看什么上下文）③（工具在哪执行）

## 三、模型是 Runtime 的一项可替换能力（2.3）

- 模型不是 DSH 本体，是 Provider / Model Adapter 挂载的能力
- 接入：设置 → 模型 → 添加自定义提供方（本地 vllm Qwen / 云端 openai-responses / openai 协议均可）

## 四、两套 Preset 对照实验（2.4）

**实验 1**：4 个 Session 分别选 Standard/PTC/Minimal/Creative，问"你当前能用哪些能力"，**从 UI/Trace 取证**（不信模型自述）

**实验 2**：同一任务（读目录→建 3 个文本文件→统计总行数→摘要）跑 4 套 Preset，比执行轨迹

表 2-2 观测项（7 个）：
1. 工具 / 能力
2. 角色设定 / Prompt
3. 计划 / 工具调用步骤
4. 自主程度 / 控制粒度
5. 状态记录 / 元信息（Trace）

> 一句话记忆：Preset 改的是"能力如何呈现"，不是"能力是否存在"——两件事。

## 五、PTC 之谜揭晓（表 2-1）

- **PTC = Standard + Code Mode 的工具呈现**，官方目录 `code/`
- Task1 猜"在机制层做文章"方向对；准确含义 = 程序化控制 + 代码模式

## 五补 · 三个设计边界（2.4.2）

| 边界 | 一句话 | 我的实验证据 |
|---|---|---|
| ① 能力存在 ≠ 能力呈现 | PTC 没删工具，只改了 model-facing presentation（摆在模型面前的样子） | write 存在但直接调报 UNKNOWN_TOOL |
| ② Preset ≠ System Prompt | Prompt 只是 Preset 五个格子（Persona / Prompt Section / Tools / agent-local Service / Runtime 行为）之一 | Creative 的 web_search 是挂载的；只在 Standard 的 Prompt 里写"你可以联网"调不出来——Prompt 只是文字，改不了挂载 |
| ③ Host 共享、Session 独立 | 1 栋楼 + 4 个房间，不是 4 栋楼 | 4 个 Session 共用一个 Web UI / 模型配置 / 工具注册表；各自有子目录和对话记录 |

- 边界③的精确区分：**机制共享，内容独立**——Persistence（存储管道）共享，但每个 Session 的记忆内容互相看不见。
- 我的思考足迹：边界②最初答"必须依赖外部工具"——方向对，补上关键一步"Prompt 只是对模型说的话，改不了挂载"；边界③最初答"记忆共享"——**反了**，记忆内容各自独立，共享的只是存储机制。错因：被"Persistence 共享"这个词误导，没区分机制与内容。

> 一句话记忆：Prompt 改"它想怎么做"，Preset 改"它能怎么做"；Host 给水电煤，Agent Scope 是自己的房间。

## 六、Benchmark 为什么选 Minimal（2.5）

| 维度 | Model Benchmark | Agent Benchmark |
|---|---|---|
| 测什么 | 纯模型能力 | 模型 + Runtime 的整体 |
| 变量 | 只有模型 | 模型、Prompt、Tool、Loop 都有 |
| DSH 选 Minimal 原因 | —— | 减少 Harness 变量，逼近"测模型" |

> 一句话记忆：模型在纯 Benchmark 上强，不代表放进任何 Runtime 都强——Harness 会放大或拖累模型。

## 自测问题（3 问的答案）

1. **为什么"模型自述能力"不能当证据？** 模型可能遗漏工具、把多项能力合并成模糊概念 → 应查 UI/Trace 的客观记录
2. **Preset 改"能力是否存在"还是"如何呈现"？** 改呈现方式，不是存在与否（两件事）
3. **纯模型强 = 任何 Runtime 都强？** 不。Model Benchmark ≠ Agent Benchmark，Harness 是关键变量

## 速记

1. 环境：Node ≥ 22.19 → `npx @deepseek-ai/dsh web` → 127.0.0.1:3080
2. Workspace = Agent 的"现实世界"，只在空目录 dsh-lab 实验
3. 模型是 Runtime 可替换的一项能力，不是 DSH 本体
4. Preset 改的是"能力如何呈现"，不是"能力是否存在"（两件事）
5. PTC = Standard + Code Mode 工具呈现（官方目录 code/）
6. Model Benchmark ≠ Agent Benchmark，官方选 Minimal 是减少 Harness 变量
7. 实证：Minimal 自述"能创建文件"，实际没有 write（echo 重定向）——**取证只看 Trace**
8. 实证：PTC 的 write 存在但直接调报 UNKNOWN_TOOL，须包进 run_code——**存在 ≠ 呈现的铁证**

## ⚠️ 待确认

~~实验 1、实验 2 数据~~、~~模型信息~~均已落实（见 homework.md）。本章无遗留问题。

## 下一步（Task3 预告）

本章看到了 4 套 Preset 的**能力差异**，但只知其然不知其所以然。带着悬念进 Task3：

1. Preset 之间的差异，**机制上在哪一层实现的**？（大概率是插件化 / Cordis 那章）
2. Trace 里看得到工具调用，但看不到背后的 hook / 拦截逻辑——这些"隐形的组织机制"怎么被揭示？
3. 亲手加一个能力（Skill/Tool/Hook），真正理解"一切皆插件"。

→ 对应学习路径第 3–5 步「亲手加能力 → Everything is a Plugin → Cordis」。
