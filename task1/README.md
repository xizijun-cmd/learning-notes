# Task1 · 为什么 Agent 不只是"大模型 + 工具"

范围：导读 0.1–0.3（为什么现在需要 Harness）+ 第 1 章 1.1–1.5（Harness 不只是另一个 Coding Agent）

> **核心一句话**：模型只生成 Token（会"说"不会"做"），Harness 把"说"变成"做"——DSH 开放的是**组织方式**，不是具体内容。

## 文件

- [notes.md](./notes.md) —— 骨架笔记（10 问 ↔ Agent System 九要素对照、插件化、稳定 vs 版本敏感）
- [concepts.html](./concepts.html) —— 概念讲解页（交互式，建议开 Pages 在线看）
- [homework-a.html](./homework-a.html) —— 作业 A：Model → Harness → 外部世界 示意图（标注 Harness 要解决的 6 个问题）
- [homework-b.md](./homework-b.md) —— 作业 B：为什么 DSH 值得单独写一本书（约 480 字）

## 速记

1. 核心矛盾：模型只生成 Token，Agent 要完成任务
2. 链条：Model（想）→ Harness（传 + 控）→ 外部世界（做）
3. Agent System 九要素，Harness 负责组织；10 问 = 逐个点名要素
4. Harness 两项职责：连接 + 约束
5. 值得写书：产品能力全进插件体系，扩展不改特权核心；但 ≠ 没有核心
6. 插件化：依赖"能力"不依赖"实现"；不是想删就删（有依赖关系）
7. "加 Tool"在工具圈做加法，"一切皆插件"把 Runtime 也开放
8. 架构语义稳定，具体实现易变——骨架放心依赖，细节别当契约

## 待后续章节回答

- PTC Preset 的具体含义（第 3 章拆解）
- 10 问中：历史状态保存、派生子 Agent、恢复 / 审计 / 测试（第 2–3 章）
