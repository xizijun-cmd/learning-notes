# Task2 · 从零开始运行 DSH

范围：第 2 章（2.1 环境 / 2.2 Workspace / 2.3 模型 / 2.4 两套 Preset 对照实验 / 2.5 Benchmark）

> **核心一句话**：Task2 是"动手"——把 DSH 跑起来，用 UI/Trace 而非模型自述，验证 Preset 到底改了什么。

## 文件

- [notes.md](./notes.md) —— 骨架笔记（Workspace、PTC 之谜揭晓、Benchmark 为什么选 Minimal）
- [concepts.html](./concepts.html) —— 概念展示页（4 套 Preset 对比 + 实验轨迹可视化，建议开 Pages 在线看）
- [homework.md](./homework.md) —— 实验记录（实验 1 能力对照 + 实验 2 轨迹差分 + 自测 3 问）

## 核心发现（实测）

1. **PTC 的铁证**：直接调 write 报 2 次 UNKNOWN_TOOL，模型自我纠正改在 run_code 内调用——Preset 改的是"呈现"不是"存在"
2. **Minimal 自述不可信**：自称"能创建文件"，实际没有 write 工具（用 echo 重定向）——取证只能看 Trace
3. **Creative 独有**：web_search / web_fetch + Skills（agent-experience）
4. 同一 MiniMax 模型，4 套 Preset 轨迹完全不同——Model Benchmark ≠ Agent Benchmark

## 环境

WSL（Ubuntu）· Node v22.23.3 · MiniMax 2.7 highspeed（云端 API）· workspace：`/home/jie/project/DSH/dsh-lab/`
