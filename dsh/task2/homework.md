# Task2 实验记录

> Task2 任务提交物：环境信息 + 两组对照实验 + 自测问答。数据来源：DSH Web UI Trace（2026-10-16）。

## 环境信息

| 项 | 值 |
|---|---|
| 运行环境 | WSL（Ubuntu，用户 jie） |
| Node 版本 | v22.23.3（npm 10.9.9 / npx 10.9.9 / git 2.43.0） |
| 启动方式 | `npx @deepseek-ai/dsh web` → 127.0.0.1:3080 |
| Workspace 目录 | `/home/jie/project/DSH/dsh-lab/`（空目录，各 Preset 子目录 standard / ptc / minimal / creative） |
| 接入模型 / 协议 | MiniMax 2.7 highspeed（云端 API；协议以 UI 配置为准） |

---

## 实验 1：4 套 Preset 能力对照

> 方法：4 个 Session 分别选 Standard / PTC / Minimal / Creative，问"你当前可以使用哪些能力"，从 UI/Trace 取证（不是问模型它自己怎么说）。
> 交叉验证：用实验 2 的实际工具调用记录，检验各 Session 的"自述"是否可信。

| Preset | 暴露的工具 / 能力（Trace 自述） | Prompt / 上下文差异 | 取证方式与交叉验证 |
|---|---|---|---|
| Standard | bash（shell 执行，cd 需 workdir 参数）、read / write / edit（本地文本文件）、glob（模式查找）、grep（正则搜索）等 | runtime-context（workspace-write 沙箱策略） | 实验 2 实际用了 bash + write，与自述一致 ✅ |
| PTC | **同一套工具（bash、read / write / edit…），但标注"通过 run_code 调用"** | Code Mode 约定 | 实验 2 直接调 write 报 UNKNOWN_TOOL、须经 run_code——自述与实际呈现一致 ✅ |
| Minimal | 清单明显更短、更笼统：文件操作（读取 / 编辑 / 创建）+ Bash 命令执行（cat, grep, ls, sed 等） | 精简 | 自述含糊地说"创建文件"，实验 2 实际**没有 write 工具**、用 echo 重定向——自述不可信的实证 ⚠️ |
| Creative | 文件操作（read / edit / write / glob / grep）+ bash + **web_search / web_fetch（网络搜索）** | runtime-context + `<system-reminder>` 声明可用 **Skills**（agent-experience） | 实验 2 未用到网络工具，自述待后续验证 |

**一句话结论**：四套 Preset 的差异不在"有没有工具"，而在**暴露了哪些工具、以什么方式呈现**——Minimal 砍掉了专项工具（自述还含糊其辞），PTC 把同一套工具包进 run_code，Creative 多给了网络能力 + Skills。模型自述与 Trace 实际对照后才发现：**Minimal 的自述最不可信**。

---

## 实验 2：同一任务 4 种模式的执行轨迹差分

> 任务：读目录 → 建 3 个文本文件 → 统计总行数 → 摘要
> 数据来源：4 个 Session 的 Trace 截图（2026-10-16）
> 注：任务未规定文件内容，故各模式行数不同（12/11/10/7），本实验比较的是**轨迹与方式**，不是数值。

| 观测项 | Standard | PTC | Minimal | Creative |
|---|---|---|---|---|
| 工具 / 能力 | bash + write | **只有 run_code**（write/read 藏在代码内部调用） | **只有 bash**（建文件用 `echo >` 重定向） | bash + write，上下文额外暴露 **Skills**（agent-experience） |
| 角色设定 / Prompt | 系统提示 + runtime-context（workspace-write 沙箱策略） | 同左 + Code Mode 约定 | 精简 | runtime-context + `<system-reminder>` 声明可用 Skills |
| 计划 / 工具调用步骤 | ls → write×3 → wc -l | 直接调 write **失败 ×2（UNKNOWN_TOOL）**→ 自我纠正 → run_code{write×3} → run_code{read×3 并计数} | ls → echo×3 → wc -l | ls → write×3 → wc -l |
| 轮次 | 约 2 轮 | **4 轮**（含失败与自我纠正） | 约 2 轮 | 约 2 轮 |
| 工具调用次数 | 5（bash1 + write3 + bash1） | 12+（失败2 + run_code4 + 子工具 write3 + read3） | 5（全 bash） | 5（bash1 + write3 + bash1） |
| 自主程度 / 控制粒度 | 粒度 = 单个工具调用 | 粒度 = **一段代码**，模型自行编排子工具；具备自我纠错 | 最粗：shell 命令一把梭 | 粒度 = 单工具，内容发挥更自由（"这是第一个。"） |
| 结果 | 3 文件共 12 行 | 3 文件共 11 行 | 3 文件共 10 行 | 3 文件共 7 行 |

**一句话结论**：4 套 Preset 跑的是同一个任务，**差别全在"工具如何呈现"**——Standard 直接给工具；PTC 把工具藏进 run_code（直接调会报 UNKNOWN_TOOL，模型要先学会新界面，轮次最多但编排力最强）；Minimal 连 write 都不给、只剩 bash；Creative 额外暴露 Skills。这正好验证了"Preset 改的是呈现，不是存在"。

---

## 自测问题（用自己的话回答）

1. 为什么"模型对自身能力的说明"不能当证据？应该看什么？

   模型的工具是 Harness 装配的，它自己并不知道被配了哪些，所以"自述"是猜的——会漏报、多报、模糊合并。比如本次实验里 Minimal 自称"能创建文件"，Trace 却显示它没有 write、是用 `echo >` 重定向。所以该看 Trace 的客观调用记录，不是模型自己怎么说。

2. Preset 改变的是"能力是否存在"还是"能力如何呈现"？两者是几件事？

   这是两件事：能力**是否存在**，和能力**如何呈现**。Preset 改的是呈现。本次实验里 PTC 的 write 明明存在（run_code 里能调），直接调却报 UNKNOWN_TOOL——存在没变，只是呈现方式变了。

3. 一个模型在纯模型 Benchmark 上很强，放进任何 Agent Runtime 都会同样强吗？为什么？

   不一定。Agent 表现 = f(模型, Harness, 工具, 上下文, 策略, 环境)，模型只是变量之一。同一个 MiniMax，塞进 Standard 和 Minimal 行为就不同。所以纯模型 Benchmark 强，不代表放进任何 Runtime 都强——这也是 Agent Benchmark 存在、且官方选 Minimal 的原因。
