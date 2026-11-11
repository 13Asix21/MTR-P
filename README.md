# MTR-P · A Knowledge–Project Governance System for Human–AI Work

**MTR-P 知识-项目体系 · 人机协作治理框架**

> Combining AI knowledge with project R&D system and principles.
> 结合 AI 的知识-项目研发体系与原则。

> `MTR` 是"我知道什么"，`P` 是"我做了什么"，中间靠知识路由双向打通。
> `MTR` is what I *know*. `P` is what I *built*. A knowledge router connects them.

---

## 这是什么 / What is this

一套 **23 条原则 × 6 层 + 5 库模型** 的治理体系，用于在 AI 深度参与研发的场景下，**让人始终保有判断力并控制住风险**。

它不是提示词模板库，也不是 Agent 框架。它回答一个问题：

> **当 AI 把你的产出放大十倍之后，你还是那个能判断对错的人吗？**

A governance framework — **23 principles across 6 layers, built on a 5-store model** — for keeping human judgment intact and risk under control when AI does much of the work.

It is **not** a prompt library and **not** an agent framework. It answers one question:

> **After AI multiplies your output tenfold, are you still the person who can tell right from wrong?**

---

## 为什么需要它 / Why

三个正在发生的失效模式：

| 失效 | 表现 |
|---|---|
| **认知投降**（Cognitive Surrender） | AI 写什么就信什么。代码能跑、测试全绿就批准。被问"为什么这么写"时答不上来。 |
| **理解债**（Comprehension Debt） | 代码越长越多，你对系统的理解没跟上。半年后出问题，没人能从第一性原理修它。 |
| **意图债**（Intent Debt） | 你没想清楚的目标和取舍，被 AI 替你悄悄决定了。 |

主流讨论集中在"模型能做什么"。我们更关心"**我们在用它做什么**"——以及做完之后，我们是不是还懂。

Three failure modes already happening:

| Failure | Symptom |
|---|---|
| **Cognitive Surrender** | Whatever the AI writes gets approved. Green tests are treated as proof. You can't answer "why is it written this way?" |
| **Comprehension Debt** | Codebase grows, your understanding doesn't. Six months later nobody can fix it from first principles. |
| **Intent Debt** | Trade-offs you never decided get silently made for you. |

Most discussion asks *what models can do*. This project asks *what we are doing with them* — and whether we still understand the result.

---

## 核心模型 / Core model

```
┌──────────────────────────────────────────────────────┐
│              KNOWLEDGE BASE  知识库                    │
│   M (Master) ──⇅── T (Trial) ──⇅── R (Rarely)         │
│   已验证            待验证           冷宫归档            │
│   RAGFlow         Obsidian        Obsidian/_archive   │
└─────────────────────────┬────────────────────────────┘
                          │
              ┌───────────▼────────────┐
              │   KNOWLEDGE ROUTER     │
              │   知识路由中间件         │
              │   Read Path + Write Path│
              └───────────┬────────────┘
                          │
┌─────────────────────────▼────────────────────────────┐
│               PROJECT BASE  项目库                     │
│   P: code / config / CI / ADR / tests / contracts    │
│   Git — single source of truth                       │
└──────────────────────────────────────────────────────┘
```

**两套完全不同的资产，不能用同一套标准管：**

| | 知识库 M / T / R | 项目库 P |
|---|---|---|
| 判断标准 | 是否可信、是否验证过 | 是否可运行、是否通过测试 |
| 演进方式 | 动态沉浮（上浮 / 下沉 / 复活） | 版本控制（commit / branch / tag） |
| 回滚方式 | 沉到 R，不删除 | `git revert` |
| 写入权限 | 人类策展 + 审批 | diff + review + merge |

> **R 库是被低估的数据源**——它记录"曾经活跃、后来放弃"，是一部负样本史。只看 M 库的成功案例，就会反复走同一条死路。

---

## 23 条原则 / The 23 principles

| 层 Layer | 条数 | 原则 Principles |
|---|---|---|
| **认知层** Mindset | 6 | 1 信息源刷新 · 2 用进废退 · 3 委托不委托判断 · 21 可信度加权 · 22 持续质疑 · 23 痛苦是信号 |
| **规范层** Discipline | 4 | 4 Spec 先行 · 5 链路深究 · 6 不懂立刻问 · 7 体系连接 |
| **操作层** Practice | 3 | 8 并行思考 · 9 快速部署 · 10 深度复盘 |
| **安全层** Guardrails | 4 | 11 分层规则 · 12 检测与可观测 · 13 最小权限 · 14 可回滚 |
| **知识库层** Knowledge Base | 5 | 15 分层检索 · 16 概念查询优先 · 17 人类策展 · 18 证据保留 · 19 知识路由 |
| **表述层** Expression | 1 | 20 表述最简洁 |

完整正文：[`PRINCIPLES-zh.md`](PRINCIPLES-zh.md) · English translation in progress ([contribute?](CONTRIBUTING.md))

---

## 30 秒起步 / Quick start in 30 seconds

不需要建五库，不需要写路由中间件。先做这三件：

**1. 给每个任务一个能跑出结果的检查**
测试 / 构建退出码 / lint / 截图。要 Agent **展示结果**，不是自述"已完成"。

**2. 把纠正过两次的东西写进指令文件**
`AGENTS.md` / `CLAUDE.md`。同一问题纠正两次 = 该固化了。

**3. 硬规则用 hook，别用大写字母**
prompt 里喊 `IMPORTANT!` 是请求，不是规则。真正的规则靠 hook / CI 强制。

No five-store setup required. Start with three moves:

1. **Give every task a check that can pass or fail** — tests, build exit codes, lint, screenshots. Ask the agent to *show the result*.
2. **Write anything you corrected twice into your instruction file** — `AGENTS.md` / `CLAUDE.md`.
3. **Enforce hard rules with hooks, not capital letters.**

---

## 目录结构 / Repository layout

```
MTR-P-System/
├── README.md                              # 本文件（中英对照）
├── README.en.md                           # English only
├── PRINCIPLES-zh.md                       # 23 条原则正文（中文）
├── CONTRIBUTING.md                        # 如何贡献 / how to contribute
├── LICENSE                                # CC BY 4.0
└── docs/
    ├── dual-track-research-system-zh.md   # 双轨研发的 AI 动态研究系统（工程类 + 工艺类）
    ├── comparison-ray-dalio-zh.md         # 与达利欧《原则》的异同对照
    ├── reference-addy-osmani-zh.md        # Addy Osmani：管理 Agent 的十条建议
    └── source-addy-osmani-en.md           # Addy Osmani 三篇原文合集（英文）
```

**不在本仓库 / out of scope**：Vibe-Coding 方法调研、需求追溯体系落地计划——相关但主题不同。

---

## 设计哲学 / Design philosophy

这四条是"元规则"，比任何单条原则都重要：

1. **模型是变量，harness 是常量**
   98% 的可靠性不在模型里，在模型周围的代码里。投资比例要对应。

2. **原则必须来自现实反馈**
   拍脑袋想出来的原则只是一堆好听的话。它必须被现实验证过、被写下来、被编码进系统、并随反馈调整。

3. **判断力不外包**
   任务可以外包，判断不行。判断和品味是最后一道不委托的工序。

4. **承认能力边界**
   加权最诚实的终点是"我判断不了"。把"不知道"伪装成"知道"，是最大的风险。

Four meta-rules that outrank any single principle:

1. **Models are the variable; the harness is the constant.** 98% of reliability lives in the code around the model.
2. **Principles must come from reality feedback.** Otherwise they're just nice-sounding words.
3. **Delegate tasks, never judgment.**
4. **Admit the boundary of competence.** The most honest end of weighting is "I can't decide."

---

## 来源与致谢 / Sources & credits

本体系是多源采撷 + 个人实践归纳，不是原创理论：

| 来源 | 贡献 |
|---|---|
| **Addy Osmani**（Anthropic, Claude Code） | 认知投降、理解债、WIP limit、工厂模型、委托不委托判断 |
| **Simon Willison** | Agentic Engineering Patterns、TDD with agents、囤积可复用解法 |
| **GitHub Blog** | Multi-agent typed schemas、Detect/Deliver 同步 |
| **Agent Standard**（Anthropic + OpenAI + Cognition + LangChain） | 确定性优先、架构胜框架、Harness > Model、Eval 驱动 |
| **OpenAI Model Spec / Anthropic Constitution** | 指令层级、歧义默认澄清、可逆操作优先 |
| **Ray Dalio《原则》** | 痛苦+反思=进步、创意择优（极度求真 + 极度透明 + 可信度加权）、原则书面化并编码 |
| **ETH Zurich 研究** | LLM 生成的 AGENTS.md 无益甚至有害，人类策展有益 |
| **Anthropic 技能形成论文** | 生成 vs 概念查询对学习的影响（−17%） |
| **Hermes-MTP 工程实践** | 双态分离、确定性 Gate、审批绑定、MTR-P 库层设计 |

个人实践部分（信息源四维刷新、MTR 动态沉浮、并行思考重定义、可信度加权取数、持续质疑、痛苦分类）来自真实项目踩坑，不是二手转述。

---

## 引用 / Citation

```
MTR-P: A Knowledge–Project Governance System for Human–AI Work (2026).
https://github.com/<your-org>/MTR-P-System
```

---

## 许可 / License

[Apache License 2.0](LICENSE) — 可自由使用、修改、分发与商用，需保留版权声明与许可副本。

Licensed under the [Apache License 2.0](LICENSE). Free to use, modify, distribute, and build on commercially; retain the copyright notice and license copy.

---

## 贡献 / Contributing

见 [CONTRIBUTING.md](CONTRIBUTING.md)。最需要的两件事：

- **英文翻译**（`PRINCIPLES.en.md` 尚未完成）
- **实践反馈**：哪条原则在你的场景失效了？怎么改的？

See [CONTRIBUTING.md](CONTRIBUTING.md). Two things we need most:

- **English translation** of `PRINCIPLES-zh.md`
- **Field reports**: which principle broke in your context, and what you changed.
