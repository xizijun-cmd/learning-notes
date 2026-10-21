# Task3 · "一切皆插件"意味着什么？

范围：第 3 章（3.1 四套 Preset 不是四个 Agent / 3.2 Host Plane 与 Agent Plane / 3.3 Standard 能力基线 / 3.4 PTC 同一批能力不同呈现 / 3.5 Minimal 把 Harness 压到非常薄）

> **核心一句话**：4 套 Preset 不是 4 个 Agent，而是同一个 Host 上挂载的不同 Agent 组合——共享的进 Host Plane，会话的进 Agent Scope，Preset 只是一张配置单。

## 文件

- [notes.md](./notes.md) —— 骨架笔记（学习目标 / 学习策略 / 四概念 / 三个判断 / 表 3-1 四类组合项 / 官方自测 3 问 / 参考作业）
- [concepts.html](./concepts.html) —— 单章概念讲解（讲解式 9 节：先例子后结论，每节一句话记忆 + 自测遮答案）
- [homework.html](./homework.html) —— 作业①：Host Plane / Agent Plane 分层图（Standard 10 项组合项 → 12 个标注条目，可直接截图）

## 本章无跑实验，有画图作业

第 3 章是纯概念章，没有让跑代码；产出是笔记 + 分层图。用 Task2 的实验结果当"实物"对照理解。

## 学习目标完成度

| 目标 | 状态 |
|---|---|
| 说清 4 套 Preset 不是 4 个 Agent | ✅ 笔记第二节 |
| 区分 Preset / Scope / Host Plane / Agent Plane | ✅ 笔记第一节 |
| 解释 Scope Context 与边界 | ✅ 笔记第四、五节 |
| 分析 Standard 能力基线与作用域 | ✅ 笔记第六节 + 分层图 |
| 对比 PTC 与 Minimal | ✅ 笔记第七、八节 |

## 速记

1. 4 套 Preset ≠ 4 个 Agent：一个 Host + 各自的 Agent Scope
2. Host Plane 管"怎么有"，Agent Plane 管"怎么用"——分工原则，不是物理边界
3. 作用域三问：属于谁 / 谁能看见 / 跟谁一起销毁
4. PTC 换呈现，不换工具；减少 round trip 是目标不是保证
5. Minimal 是观察基线：模型看见的少，底层治理还在

## 交付清单

| 任务要求 | 落在哪 |
|---|---|
| 学习目标 5 条 | notes.md 开头 + 本页完成度表 |
| 参考作业① 分层图 | homework.html |
| 参考作业② 两类需求 | notes.md「参考作业」 |
| 自测 3 问 | notes.md「官方自测 3 问」+ concepts.html 第 8 节 |
