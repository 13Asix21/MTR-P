---
type: comparison
status: draft
tags:
  - principles
  - MTR-P
  - Ray-Dalio
  - decision-making
  - knowledge-base
created: 2026-11-11
source: Internal personal knowledge base retrieval · Ray Dalio, *Principles* (CITIC edition)
related:
  - "[[PRINCIPLES-en]]"
---

[中文版](comparison-ray-dalio-zh.md) · [← Back to README](../README.en.md)

# Ray Dalio's *Principles* vs. MTR-P: A Comparison

> **Evidence basis**: full text retrieved via `search_chunks` + `ask_question` against an internal knowledge base, citing chunks directly rather than secondhand summaries.

---

## 1. The skeleton of Dalio's system

### 1.1 Meta-assumption layer

**Embrace reality and deal with it** — life principle #1. The core mechanism, quoted from the text:

> Reality will send you strong signals — rewarding you or punishing you — to reflect how well your principles are working. That way you can learn to adjust those principles accordingly.

> **Pain + Reflection = Progress**

The origin of a principle is not deduction but **feedback → adjustment**. This shares DNA with our principle 1 (refresh your sources), except Dalio's feedback loop is harder — he uses **actual P&L** as the referee.

### 1.2 Operating layer: the five-step process

```
Goals → Problems → Diagnosis → Design → Doing
```

Life principle #2. The text stresses: no matter how well designed, the machine will break; you must find the problem, open the hood, and diagnose the root cause. The diagnostician must understand every component — **design and people**. **People are the most important factor.**

### 1.3 Decision layer: idea meritocracy

The formula from the text:

```
Idea meritocracy = Radical truth + Radical transparency + Believability-weighted decisions
```

Operating in three steps:

1. Put your view on the table openly
2. Discuss the disagreement seriously
3. Follow the resulting consensus and close the disagreement

Dalio's definition of **radical transparency**:

> I mean letting almost everyone see almost everything. If people have incomplete information, they are easily influenced and cannot form their own judgments. Radical transparency reduces the harm of office politics and lowers the risk of misconduct, because misconduct tends to happen behind closed doors — in the open there is nowhere to hide.

### 1.4 Organizational view: the machine model

```
Goals → Machine (design + people) → Outcomes
```

A manager doesn't do the work; a manager **fixes the machine**. Design and people are two independently diagnosable components.

### 1.5 Cognitive prerequisites

- **Be radically open-minded** (life principle #3) — counter confirmation bias, the "two yous" (the higher-level you vs. the emotional you)
- **Understand that people are wired differently** (#4) — neural wiring determines role fit
- **Learn how to make decisions effectively** (#5) — expected value, prioritization, 80/20

### 1.6 How it lands: write it down and encode it

Key passage:

> We wrote these decision principles down in writing, then **encoded them into computers** and made decisions accordingly.
> This decision process brought us both successes and failures, which gave us more opportunities to learn lessons — **and those lessons became more principles, which were encoded into the system** to guide practice.

---

## 2. Isomorphic zone: both say the same thing

| # | Dalio | MTR-P | Degree |
|---|---|---|---|
| 1 | Pain + Reflection = Progress | Principle 10 (deep retrospective) + trusted chain | **Strong**. Dalio treats pain as a positive signal; we treat failed chains as samples |
| 2 | Five-step process | Factory model (Plan → Spawn → Verify → Integrate → Retro) | **Strong**. His "diagnosis" maps to our "Verify", his "doing" to "Integrate" |
| 3 | Write principles down → encode into computers | AGENTS.md / rules.yaml / hooks / CI | **Nearly identical**. Dalio was encoding principles into decision systems in the 2010s |
| 4 | Radical transparency | Principle 12 (observability) + 18 (evidence retention) | **Isomorphic, different carrier**: he exposes to people, we leave traces for machines |
| 5 | Radical truth (don't filter out hard problems) | Principle 5 ("a green check only proves what it tested") | **Strong** |
| 6 | Don't blindly follow anyone's principles | Principle 17 (human curation) / don't blindly trust AI | **Isomorphic**. Dalio: "I don't want you to blindly follow my principles, or anyone else's" |
| 7 | Machine view: goals → design + people → outcomes | MTR-P's four stores + knowledge router *are* the machine | **Isomorphic, different object**: he fixes organizations, we fix a knowledge-project pipeline |
| 8 | Face the problem, open the hood, diagnose | Principle 5B (dig into engineering trade-offs, derive success/failure chains) | **Isomorphic** |

---

## 3. Divergence zone

### 3.1 Different objects, different risks

| Dimension | Dalio | MTR-P |
|---|---|---|
| Object | People + organizations | People + AI + knowledge and project assets |
| Core risk | Confirmation bias, office politics, emotional decisions | **Cognitive surrender, comprehension debt, intent debt**, uncontrolled output |
| Decision-maker | A group (believability-weighted) | One person + multiple agents |
| Time scale | Decades of organizational evolution | Single task to quarterly |
| Referee | Actual P&L (hard) | Tests / evidence / approvals (also hard, but narrower coverage) |

**The essential gap**: Dalio's system has **no concept of "AI eating your judgment."** He deals with people avoiding the truth; we also deal with people who never had to face it, because AI already decided for them.

### 3.2 What we have that he doesn't

| Capability | Principles | Why Dalio lacks it |
|---|---|---|
| **Asset tiering and lifecycle** (MTR floating, archive revivable) | 2, 15, 19 | His principle library is "written down" — no automatic demotion or revival |
| **AI-specific risk taxonomy** (offload → transfer → surrender) | 2B, 3, 4 | Different era |
| **Enforcement that doesn't rely on discipline** (hooks / CI / router cannot overreach) | 11, 13, 19 | Bridgewater relied on culture and tools; culture depends on people. Hooks don't forget |
| **Byte-level auditable chain** (SHA-256 + decision log) | 12, 18 | His transparency is "people can see"; ours is "machines can verify" |

### 3.3 What he has that we lack

| Gap | Dalio's approach | Our current state |
|---|---|---|
| **① Believability-weighted decisions** | Weight by a person's **track record of being validated in a specific domain**. Disagreements aren't settled by vote or rank | **Complete blank**. Principle 8 only says "have your own hypothesis first" — it never says what to do when your hypothesis conflicts with the AI's |
| **② Radical open-mindedness (chapter-level)** | A full life principle with the "two yous" framework, thoughtful disagreement, and a diagnostic table for "why you don't want to hear opposing views" | Scattered across principles 5 and 6; no standalone **anti-confirmation-bias** mechanism |
| **③ A psychological mechanism for facing pain** | Treats pain as a signal rather than something to avoid, with a full emotion–reflection–progress chain | Only Osmani's "notice when you're tired"; no positive framing |

---

## 4. The most important gap to close: credibility weighting

### 4.1 The problem

Principle 8 requires: "before handing a task to AI, you should already have a hypothesis."

It does **not** answer: when my hypothesis conflicts with the AI's output, whose wins?

Today the answer is implicit and vibes-based — sometimes blindly trusting yourself, sometimes blindly trusting the AI. That is exactly what principle 3 guards against, but there's no **operational rule for resolving disagreement**.

### 4.2 Dalio's approach

> Make decisions based on the believability of the viewpoint.

Believability = a person's **number of validated judgments in a specific domain** ÷ total judgments. Disagreements are weighted, not settled by rank or volume.

### 4.3 Proposed mapping into MTR-P

Maintain a domain-level credibility score per source:

| Source | Domain | Initial weight | Update rule |
|---|---|---|---|
| Own judgment | Architecture | 0.7 | Verified correct +0.02; wrong −0.05 |
| AI output | Code implementation | 0.8 | −0.08 per reverted PR |
| AI output | Architecture | 0.4 | Heavy cargo-cult history |
| M store entry | General knowledge | 0.9 | Already verified |
| T store entry | General knowledge | 0.5 | Unverified |
| R store entry | Any | 0.3 | Once judged not worth using |
| P store ADR | Project decisions | 0.95 | Highest within project |

**Disagreement resolution rule**:

- Weight gap **> 0.3** → higher-weight side wins, but **the disagreement is recorded** (goes to T store, tagged `disputed`)
- Weight gap **≤ 0.3** → trigger "thoughtful disagreement": each side writes one thing they're most likely wrong about, then decide
- **Irreversible operation** → regardless of weight, the human has a veto

### 4.4 Implementation cost

- MVI stage: static weight table, maintained by hand
- Advanced: the router's `decision-log` is a natural source of credibility data — every AI output reverted or accepted is countable

---

## 5. Conclusion

**They are different species. Don't force a merger.**

| | Dalio's *Principles* | MTR-P |
|---|---|---|
| Essence | **Decision philosophy** (how a person decides well under uncertainty) | **Collaboration engineering + cognitive anti-degradation** (how a person stays capable of judging after AI amplifies them) |
| Main lever | Culture + an encoded principle system | Gates + chains + asset tiering |
| Strength | Decision quality, organizational consensus, psychological mechanisms | Enforcement, AI-risk taxonomy, asset governance |
| Weakness | No AI risk dimension, no asset lifecycle | Missing credibility weighting, no standalone anti-confirmation-bias principle |

**Three complementary moves** (by priority):

1. **Add credibility weighting** (high) — closes the "how do I resolve my disagreement with AI" gap
2. **Extract "actively seek opposing views" into a standalone principle** (medium) — borrowed from radical open-mindedness
3. **Write "pain is a signal" into principle 10** (low) — one line; changes the mindset, not the process

---

## Key takeaway

> Dalio started encoding principles into computers forty years ago — he was building an early human-machine decision system. But he solved **how multiple people reach consensus**. Our generation has an extra layer to solve: **how one person, after being amplified by AI, remains the person capable of judging.**
>
> His "radical transparency" and our "observability" are the same intuition implemented two ways: **concealment produces error; exposure suppresses it.** His exposure relies on people looking; ours relies on machines verifying.
>
> What's most worth copying isn't any specific entry — it's his attitude toward **the idea of "a principle"**: it must come from reality feedback, be written down, be encoded into the system, and be revised by feedback. Otherwise it's just a pile of nice-sounding words.
