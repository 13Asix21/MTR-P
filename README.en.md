# MTR-P · A Knowledge–Project Governance System for Human–AI Work

> `MTR` is what I *know*. `P` is what I *built*. A knowledge router connects them.

---

## What is this

A governance framework — **23 principles across 6 layers, built on a 5-store model** — for keeping human judgment intact and risk under control when AI does much of the work.

It is **not** a prompt library and **not** an agent framework. It answers one question:

> **After AI multiplies your output tenfold, are you still the person who can tell right from wrong?**

---

## Why

Three failure modes already happening:

| Failure | Symptom |
|---|---|
| **Cognitive Surrender** | Whatever the AI writes gets approved. Green tests are treated as proof. You can't answer "why is it written this way?" |
| **Comprehension Debt** | Codebase grows, your understanding doesn't. Six months later nobody can fix it from first principles. |
| **Intent Debt** | Trade-offs you never decided get silently made for you. |

Most discussion asks *what models can do*. This project asks *what we are doing with them* — and whether we still understand the result.

---

## Core model

```
┌──────────────────────────────────────────────────────┐
│              KNOWLEDGE BASE                           │
│   M (Master) ──⇅── T (Trial) ──⇅── R (Rarely)         │
│   verified          unverified        archived        │
│   RAGFlow           Obsidian          Obsidian/_arch  │
└─────────────────────────┬────────────────────────────┘
                          │
              ┌───────────▼────────────┐
              │   KNOWLEDGE ROUTER     │
              │   Read Path + Write Path│
              └───────────┬────────────┘
                          │
┌─────────────────────────▼────────────────────────────┐
│               PROJECT BASE                            │
│   P: code / config / CI / ADR / tests / contracts    │
│   Git — single source of truth                       │
└──────────────────────────────────────────────────────┘
```

**Two fundamentally different asset classes — never govern them with the same rules:**

| | Knowledge M / T / R | Project P |
|---|---|---|
| Test of quality | Is it trustworthy? Verified? | Does it run? Does it pass tests? |
| Evolution | Dynamic floating (promote / demote / revive) | Version control (commit / branch / tag) |
| Rollback | Demote to R — never delete | `git revert` |
| Write access | Human curation + approval | diff + review + merge |

> **The R store is an underrated data source** — it records what was once active and later abandoned. That's a history of negative samples. Look only at M's successes and you'll walk the same dead end repeatedly.

---

## The 23 principles

| Layer | Count | Principles |
|---|---|---|
| **Mindset** | 6 | 1 Refresh sources · 2 Use it or lose it · 3 Delegate tasks, not judgment · 21 Credibility weighting · 22 Continuous challenge · 23 Pain is a signal |
| **Discipline** | 4 | 4 Spec first · 5 Trace the chain · 6 Ask immediately · 7 Connect to the system |
| **Practice** | 3 | 8 Parallel thinking · 9 Deploy fast · 10 Deep retrospective |
| **Guardrails** | 4 | 11 Layered rules · 12 Detect & observe · 13 Least privilege · 14 Reversibility |
| **Knowledge Base** | 5 | 15 Layered retrieval · 16 Concept before generation · 17 Human curation · 18 Evidence retention · 19 Knowledge routing |
| **Expression** | 1 | 20 Maximum concision |

Full text (Chinese): [`PRINCIPLES-zh.md`](PRINCIPLES-zh.md) · English translation in progress — [contributions welcome](CONTRIBUTING.md)

---

## Quick start in 30 seconds

No five-store setup required. Start with three moves:

1. **Give every task a check that can pass or fail** — tests, build exit codes, lint, screenshots. Ask the agent to *show the result*, not claim completion.
2. **Write anything you corrected twice into your instruction file** — `AGENTS.md` / `CLAUDE.md`.
3. **Enforce hard rules with hooks, not capital letters.** `IMPORTANT!` in a prompt is a request. A hook is a rule.

---

## Repository layout

```
MTR-P-System/
├── README.md                              # bilingual
├── README.en.md                           # this file
├── PRINCIPLES-zh.md                       # the 23 principles, full text (zh)
├── CONTRIBUTING.md
├── LICENSE                                # CC BY 4.0
└── docs/
    ├── dual-track-research-system-zh.md   # AI research system for engineering + process R&D
    ├── comparison-ray-dalio-zh.md         # vs. Ray Dalio's *Principles*
    ├── reference-addy-osmani-zh.md        # Addy Osmani: 10 rules for managing agents
    └── source-addy-osmani-en.md           # Addy Osmani: three essays, full text
```

---

## Design philosophy

Four meta-rules that outrank any single principle:

1. **Models are the variable; the harness is the constant.**
   98% of reliability lives in the code around the model. Invest proportionally.

2. **Principles must come from reality feedback.**
   Otherwise they're just nice-sounding words. Real principles get tested, written down, encoded into the system, and revised by feedback.

3. **Delegate tasks, never judgment.**
   Judgment and taste are the final step you never outsource.

4. **Admit the boundary of competence.**
   The most honest end of weighting is "I can't decide." Dressing up "I don't know" as "I know" is the biggest risk of all.

---

## Sources & credits

This is a synthesis, not original theory:

| Source | Contribution |
|---|---|
| **Addy Osmani** (Anthropic, Claude Code) | Cognitive surrender, comprehension debt, WIP limits, factory model |
| **Simon Willison** | Agentic Engineering Patterns, TDD with agents, hoarding reusable solutions |
| **GitHub Blog** | Multi-agent typed schemas, Detect/Deliver parity |
| **Agent Standard** (Anthropic + OpenAI + Cognition + LangChain) | Determinism by default, architecture over framework, harness > model, eval-driven |
| **OpenAI Model Spec / Anthropic Constitution** | Instruction hierarchy, clarify-on-ambiguity, reversible-first |
| **Ray Dalio, *Principles*** | Pain + Reflection = Progress; idea meritocracy = radical truth + radical transparency + believability-weighted decisions |
| **ETH Zurich research** | LLM-generated AGENTS.md offers no benefit; human-curated does |
| **Anthropic skill-formation paper** | Generation vs. conceptual inquiry: −17% on comprehension |

The practitioner-side principles (four-dimensional source refresh, MTR dynamic floating, credibility data from T/R stores, structured challengers, pain classification) come from real project failures, not secondhand summary.

---

## License

[Apache License 2.0](LICENSE) — free to use, modify, distribute, and build on commercially; retain the copyright notice and license copy.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Two things we need most:

- **English translation** of `PRINCIPLES-zh.md`
- **Field reports**: which principle broke in your context, and what you changed.
