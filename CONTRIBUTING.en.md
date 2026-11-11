# Contributing

Thanks for your interest. The value here isn't "yet another set of principles" — it's whether these principles survive contact with *your* context.

[中文版](CONTRIBUTING.md) · [← Back to README](README.en.md)

---

## What we need most

### 1. Field reports

More valuable than code: **which principle broke in your context, and what you changed.** Open an issue using this template:

```markdown
## Context
(project type, how many parallel agents, rough scale)

## Principle that failed
Principle X — title

## What actually happened
(not "it didn't feel right" — describe the specific failure)

## Your adjustment
(rewrote the rule? deleted it? added a precondition?)

## Evidence
(metrics, logs, before/after, revert counts…)
```

### 2. Translations

The canonical text is Chinese (`PRINCIPLES-zh.md`). When translating, keep these things stable:

- **Don't translate word-for-word.** Headings are compressed on purpose — keep the density (see principle 20).
- **Fixed terminology:**

| Chinese | English |
|---|---|
| 认知投降 | Cognitive Surrender |
| 理解债 | Comprehension Debt |
| 意图债 | Intent Debt |
| 可信度加权 | Credibility Weighting |
| 用进废退 | Use it or lose it |
| 链路深究 | Trace the chain |
| 人类策展 | Human curation |
| 可回滚 | Reversibility |
| 知识路由 | Knowledge routing |

- Name translated files `<NAME>.md` with a `.en` / `.zh` marker and update the README links.

---

## PR rules

This repo governs itself with its own principles. Please follow them.

1. **One change per PR.** Don't mix principle edits, README edits, and restructuring in a single PR.
2. **Rule changes need justification.** Explain *why the existing rule failed*, not "I think it should be this way."
3. **New principles have a bar.** There are already 23 — that's plenty. A new one must satisfy all three:
   - ① No existing principle covers it
   - ② Validated in a real scenario
   - ③ Links to at least one existing principle
4. **Don't submit large LLM-generated blobs.** See principle 17 (human curation) — AI-written rule files measurably provide no benefit and can be harmful.
5. **Deleting a principle needs more justification than adding one.** Explain where it isn't just useless but actively misleading.

---

## Commit messages

```
<type>: <short description>

type: feat | fix | docs | refactor | translate
```

Example: `docs: add R-store examples to principle 21`

---

## Code of conduct

- Critique principles, not people.
- "This didn't work for me because…" beats "this is wrong" ten times over.
- Unverifiable claims are not accepted.
