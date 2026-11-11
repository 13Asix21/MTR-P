# 贡献指南 / Contributing

[English version](CONTRIBUTING.en.md) · [← 返回 README](README.md)

感谢你有兴趣参与。这个项目的价值不在于"又多了一套原则"，而在于**这些原则是否经得起别人场景的检验**。

Thanks for your interest. The value here isn't "yet another set of principles" — it's whether these principles survive contact with *your* context.

---

## 最需要的两类贡献 / What we need most

### 1. 英文翻译 / English translation

`PRINCIPLES-zh.md` 目前只有中文。翻译时请注意：

- **不要逐字直译。** 原则的标题是压缩过的表达，英文需要保留同样的密度（见原则 20）
- **术语保持稳定**：认知投降 = Cognitive Surrender；理解债 = Comprehension Debt；意图债 = Intent Debt；可信度加权 = Credibility Weighting
- 翻译完成后命名为 `PRINCIPLES.en.md`，并在 README 更新链接

`PRINCIPLES-zh.md` is Chinese-only right now. When translating:

- **Don't translate word-for-word.** Headings are compressed on purpose — keep the density (see principle 20)
- **Keep terminology stable** (see terms above)
- Name it `PRINCIPLES.en.md` and update the README link

### 2. 实践反馈 / Field reports

比贡献代码更有价值的是告诉我们：**哪条原则在你的场景里失效了，你怎么改的。**

格式（开 Issue 即可）：

```markdown
## 场景
（什么类型的项目、多少并行 agent、什么规模）

## 失效的原则
原则 X — 标题

## 具体表现
（不要写"感觉不太好用"，写具体发生了什么）

## 你的调整
（改了规则？删了这条？加了新的前置条件？）

## 证据
（数据、日志、前后对比、revert 次数…）
```

More valuable than code: **which principle broke in your context, and what you changed.** Use the template above.

---

## 提 PR 的规矩 / PR rules

这个仓库用自己的原则管自己 —— 请遵守：

1. **一次只改一件事。** 不要一个 PR 同时改原则正文、README 和目录结构
2. **改原则要附理由。** 说清楚"为什么原来那条不够用"，而不是"我觉得应该这样"
3. **新增原则有门槛。** 现有 23 条已经不少。新增需要满足：① 现有原则无法覆盖 ② 在真实场景验证过 ③ 能与至少一条已有原则建立关联
4. **不要用 LLM 生成大段内容直接提交。** 见原则 17（人类策展）——AI 生成的规则文件实测无益甚至有害
5. **删除原则比新增更需要理由。** 说明它在什么场景下不仅无用而且误导

This repo governs itself with its own principles:

1. **One change per PR.** Don't mix principle edits, README edits, and restructuring.
2. **Rule changes need justification** — why the existing rule failed, not "I think it should be this way."
3. **New principles have a bar**: no existing principle covers it, validated in a real scenario, links to at least one existing principle.
4. **Don't submit large LLM-generated blobs.** See principle 17 (human curation).
5. **Deleting a principle needs more justification than adding one.**

---

## 提交信息 / Commit messages

```
<type>: <简短说明>

type: feat | fix | docs | refactor | translate
```

例：`docs: 补充原则 21 的 R 库取数示例`

---

## 行为准则 / Code of conduct

- 批评原则，不批评人
- "这条在我这里不管用，因为…" 比 "这条错了" 有用十倍
- 不接受无法验证的主张

- Critique principles, not people.
- "This didn't work for me because…" beats "this is wrong" ten times over.
- Unverifiable claims are not accepted.
