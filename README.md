# learning-notes

个人学习笔记仓库：把每次系统学习的成果沉淀成可检索、可分享的内容。

## 怎么组织的

```
learning-notes/
├── README.md          ← 本文件：总索引
└── <项目>/
    └── <task 或章节>/
        ├── README.md             ← 速览 + 文件索引
        ├── notes.md              ← 骨架笔记
        ├── concepts.html         ← 概念讲解页（可交互）
        └── homework-*.md/html    ← 作业
```

规矩：

1. 一层是项目，二层是 task / 章节，文件名用英文（Pages 链接友好）。
2. 每个 task 固定四件套：`notes` / `concepts` / `homework` / `README`。
3. 原料（初稿、思维导图）留在本地笔记工具里，仓库只放整理好的成品。
4. main 分支直接提交，个人笔记不搞分支流。

## 项目索引

| 项目             | 状态  | 说明                                           |
| -------------- | --- | -------------------------------------------- |
| [dsh/](./dsh/) | 进行中 | 《DeepSeek Harness 技术入门与架构原理》· Datawhale 组队学习 |

### dsh · DeepSeek Harness

官方课程：<https://www.datawhale.cn/learn/content/381/7900?from=kkframenew>



- **Task1 · 为什么 Agent 不只是"大模型 + 工具"**（导读 + 第 1 章）→ [dsh/task1/](./dsh/task1/)
  - 核心一句话：模型只生成 Token，Harness 把"说"变成"做"——DSH 开放的是组织方式，不是具体内容。
- **Task2 · 从零开始运行 DSH**（第 2 章）→ [dsh/task2/](./dsh/task2/)
  - 核心一句话：把 DSH 跑起来，用 UI/Trace 而非模型自述，验证 Preset 到底改了什么。
- **Task3 · "一切皆插件"意味着什么**（第 3 章）→ [dsh/task3/](./dsh/task3/)
  - 核心一句话：4 套 Preset 不是 4 个 Agent，而是同一个 Host 上挂载的不同 Agent 组合——共享进 Host，会话进 Scope。
- **Task4 · 一切皆插件的收尾与前三章复盘**（第 3 章 3.6–3.9）→ [dsh/task4/](./dsh/task4/)
  - 核心一句话：Creative 让 Agent 能改承载自己的 Runtime；而"一切皆插件"不散架，是因为还有只管规则的 Cordis Kernel。

### 综合复习

- **[REVIEW.md](./dsh/REVIEW.md)** —— Task1–4 全部内容的大汇总：九块核心知识、六对关键区分、术语速查、16 条速记、自测汇总、我的实验证据、复习路线

## 在线阅读

已开启 GitHub Pages：

- 首页：https://xizijun-cmd.github.io/learning-notes/
- Task1 概念讲解页：https://xizijun-cmd.github.io/learning-notes/dsh/task1/concepts.html
- Task1 作业A 示意图：https://xizijun-cmd.github.io/learning-notes/dsh/task1/homework-a.html
- Task2 概念展示页：https://xizijun-cmd.github.io/learning-notes/dsh/task2/concepts.html
- Task3 概念展示页：https://xizijun-cmd.github.io/learning-notes/dsh/task3/concepts.html
- Task3 分层图（作业①）：https://xizijun-cmd.github.io/learning-notes/dsh/task3/homework.html
- Task4 概念展示页：https://xizijun-cmd.github.io/learning-notes/dsh/task4/concepts.html
- Task4 概念地图（作业A）：https://xizijun-cmd.github.io/learning-notes/dsh/task4/homework-a.html
