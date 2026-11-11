---
type: note
tags:
  - AI-Agent
  - agent-management
  - Claude-Code
  - vibe-coding
created: 2026-11-11
source: https://x.com/addyosmani/status/2117512471930167377
related: "[[PRINCIPLES-zh]]"
---

[English version](reference-addy-osmani-en.md) · [← 返回 README](../README.md)

# Addy Osmani：管理 Agent 的十条建议

> **来源**：Addy Osmani（Anthropic Claude Code 团队）2026-11-08 X 长文《Your coding agents made you a manager》
> **原文链接**：[x.com/addyosmani](https://x.com/addyosmani/status/2117512471930167377)
> **博客延伸**：[addyosmani.com/blog/coding-agents-manager](https://addyosmani.com/blog/coding-agents-manager)
> **核心论点**：天天指挥 Agent 写代码的工程师，本质上已经在做管理了。从 Agent 中获益最大的人，不一定是 Prompt 写得最好的，而是最懂管理的人。

---

## 十条建议（按影响力排序）

### 1. 给 Agent 一个可运行的验证方式

> 最高杠杆的习惯。

- 让 Agent 有明确的 pass/fail 检查手段：测试套件、构建退出码、linter、截图对比
- 要求 Agent **展示验证结果作为证据**，而不是自说自话"我完成了"
- 工具落地：`/goal` 让独立模型每轮检查目标是否达成；Stop hook 阻止本轮结束直到 typecheck 通过；`/code-review` 把 diff 发给一个从未见过对话上下文的 sub-agent 审查

### 2. 说清楚 Agent 无法从仓库中读到的上下文

- Agent 能读到代码，但读不到你的意图、约束、业务背景
- 主动声明：这个功能面向谁、解决什么问题、哪些事不要做、做到什么程度算完成
- 写 Prompt 前先用自己的话列一份简短需求说明——避免**意图债（Intent Debt）**：没说清的目标和取舍，会被 AI 替你决定

### 3. 把重复纠正的东西写进 CLAUDE.md

- 同一个问题纠正了两次？该写进 `CLAUDE.md`（或 `AGENTS.md`）
- 系统性沉淀：模式、坑、风格偏好——每次会话都读，每次会话都更新，知识复利
- 重复的工作流应固化为 **skill**

### 4. 只用 hooks 强制执行硬规则

**两层规则体系**（关键区分）：

| 层级 | 性质 | 举例 |
|---|---|---|
| **请求层**（agent 可能权衡） | 软约束 | prompts、CLAUDE.md、skills |
| **强制层**（始终执行） | 硬约束 | hooks、deny rules、managed settings |

- 不要在 prompt 里靠 ALL CAPS 或"think harder"来强调——那是旧习惯，该丢掉
- 真正不能违反的规则，用 hook 强制（如：task 完成时自动跑测试，不通过不让结束）

### 5. 写代码之前先对齐计划

- 在 plan mode 中先就方案达成一致，再开始写
- 计划应包含：预期结果、约束条件、验收标准
- 强工程师从 Agent 中获得**更多**杠杆，而非更少——因为 spec 质量决定输出质量
- 模糊的 spec 会通过几十个并行 run 放大错误；精确的 spec 会放大成精确的实现

### 6. 委托任务，但不委托判断

> "委托任务，不委托判断。判断和品味，是最后一道不委托的工序。"

- 安全委托：写文档、检查测试覆盖、低风险机械改动
- 必须亲自把关：复杂问题、涉及认证/安全/金融/系统权限的操作
- **认知投降（Cognitive Surrender）三阶段**：
  1. 认知卸载——把具体工作交给 AI，但仍理解任务、能判断结果
  2. 判断让渡——AI 答案成为下一步工作的默认依据
  3. 认知投降——连本应自己完成的判断也拱手交出
- 长期后果：**理解债（Comprehension Debt）**——代码越来越多，但你对系统的理解没跟上

### 7. 两轮纠正失败后，重置会话

- 连续两次纠正同一个问题仍然不对 → 清除会话、重写 prompt
- 不要在烂上下文上反复堆叠提示词——那只会让 Agent 在错误方向上越走越远
- 如果连续调整两轮 Prompt 仍得不到想要的结果，回头重新梳理需求，而不是继续堆 Prompt

### 8. 分层拆解：sub-agent / agent team / worktree 隔离

- **Sub-agent** 用于分解：spawn 聚焦的子 agent，每个有明确的 brief 和文件所有权
- **Agent Team** 用于并行：lead + 若干 teammate，共享任务列表协调
- **Git worktree** 用于隔离：每个 agent 一个独立 worktree，零合并冲突
- 任务粒度：太小→协调开销主导；太大→太久不汇报有浪费风险。甜蜜区 = 自包含单元 + 清晰可交付物。每个 teammate 5-6 个任务为宜
- **一个文件只有一个 owner**——两个 agent 编辑同一文件 = 覆盖事故

### 9. 设 WIP 上限 + 定义 kill 条件

- **WIP limits**：限制同时运行的 agent 数量，3-5 个是甜蜜区。超过后会 drowning in reviews
- **Kill criteria**：agent 卡在同一个错误上 3+ 轮 → 停止、重新分配
- Addy 自述：通常跑 4-5 个 background agent + 几个 local session，有些日子已经太多
- 超过最初几个 agent 后的注意力成本 = **编排税（Orchestration Tax）**
- 审不过来时：要么补自动化检查，要么减少并行数。**"当你生成的内容多到无法负责时，你做的就不再是审查，而是在赌它没问题。"**

### 10. 像管理者一样复盘（Retro）

六步生产流水线：

```
Plan → Spawn → Monitor → Verify → Integrate → Retro
```

| 步骤 | 要点 |
|---|---|
| **Plan** | 写 spec + 验收标准。spec 是杠杆 |
| **Spawn** | 创建团队、分配 agent、明确边界 |
| **Monitor** | 每 5-10 分钟检查进度，解决阻塞。不要 hover |
| **Verify** | 跑测试、review 代码。**验证是瓶颈，不是生成** |
| **Integrate** | 合并分支、解决冲突。注意边界违反 |
| **Retro** | 更新 AGENTS.md / CLAUDE.md。知识复利 |

- 衡量指标：PR cycle time、rework、reverts——而不是"AI 写了多少代码"
- **让 Agent 反过来考你**：大型改动完成后，让 Agent 总结改了什么、为什么这么改，然后给你出几道题。全部答对才能合并代码。这样你才真正理解自己批准了什么
- **每周至少一次脱离 Agent 手搓代码**，校准自己的真实水平

---

## 补充：认知投降的防御清单

Addy 在博客《Cognitive Surrender》中给出的日常自测方法：

1. **提问前先写假设**——你认为会发生什么？哪怕猜得不好
2. **AI 交卷后找盲区**——写下一件它可能没检查到的事
3. **追问隐藏决策**——"你做了哪些我没有明确授权的决定？""哪些地方依赖了假设？""哪些改动可能影响其他功能？"
4. **不只看绿色对勾**——它检查了什么？又没检查什么？先看运行了什么、返回了什么，再看总结
5. **保留原始材料**——多 Agent 接力时，一个 agent 的总结成为下一个的输入，信息会越传越薄。重要任务必须保留原始资料和验证依据
6. **不愿署名就不交付**——设定标准，坚持下去

---

## 关键认知沉淀

> Agent 编排本质是管理技能的延伸：清晰表达、有效委派、反馈闭环、异步沟通。你不再只是写代码的人——你在建一座生产软件的工厂。工厂需要质检、流程文档、精确的输入规格。**spec 不再是 prompt，spec 是产品思考的显式表达。**
