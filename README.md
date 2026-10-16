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

## 在线阅读

已开启 GitHub Pages：

- 首页：https://xizijun-cmd.github.io/learning-notes/
- Task1 概念讲解页：https://xizijun-cmd.github.io/learning-notes/dsh/task1/concepts.html
- Task1 作业A 示意图：https://xizijun-cmd.github.io/learning-notes/dsh/task1/homework-a.html
- Task2 概念展示页：https://xizijun-cmd.github.io/learning-notes/dsh/task2/concepts.html
