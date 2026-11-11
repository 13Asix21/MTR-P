---
type: doc
tags:
  - AI-Agent
  - agent-management
  - principles
  - knowledge-base
  - MTR-P
  - risk-control
created: 2026-11-11
updated: 2026-11-11
source:
  - Addy Osmani: Cognitive Surrender / Coding Agents Need a Manager / Code Agent Orchestra
  - GitHub Blog: Multi-agent workflows / Agentic engineering system
  - Simon Willison: Agentic Engineering Patterns
  - Agent Standard (Anthropic + OpenAI + Cognition + LangChain)
  - OpenAI Model Spec / Anthropic Constitution for Claude
  - Ray Dalio, Principles
  - Internal engineering practice
related:
  - "[[reference-addy-osmani-en]]"
  - "[[source-addy-osmani-en]]"
---

[中文版](PRINCIPLES-zh.md) · [← Back to README](README.en.md)

# MTR-P Knowledge–Project System: Principles for AI-Assisted R&D

> **Name**: **MTR-P Knowledge–Project System**
> **Goal**: Stay in a state where you can *judge correctly* and *keep risk under control* while working with agents, knowledge bases (the MTR tiers), project repositories (P), and other AI R&D tools.
> **Scope**: Daily agent orchestration, MTR dynamic floating, P-store asset governance, knowledge base construction.
> **Core stance**: Models are the variable; the harness is the constant; principles are the foundation. Invest proportionally — spend less time tuning prompts, more time building systems, gates, and accumulated knowledge.

> **On the name**: an earlier internal shorthand called this the "MTP system" (three stores M/T/P). The formal name is **MTR-P** — because **M/T/R are the same kind of thing (three tiers of a knowledge base)**, while **P is a different kind of thing (a project store)**. Writing it as `MTR-P` keeps the full three-tier knowledge structure (including the R archive layer) while using a hyphen to separate the two asset classes.
>
> **One-line mnemonic**: `MTR` is *what I know*; `P` is *what I built*; a knowledge router connects them.

---

## System overview

**MTR-P** consists of **two fundamentally different asset groups** — the **knowledge base** (M/T/R) and the **project base** (P) — connected by a **knowledge routing middleware**.

```
Knowledge Base
├── M (Master)   verified      → vector store (read-only)
├── T (Trial)    unverified    → personal Obsidian knowledge base
└── R (Rarely)   archived      → personal knowledge base _archive
        ⇅ dynamic floating: promote / demote / revive
                │
     Knowledge Router
     Read Path + Write Path
                │
Project Base
└── P: code / config / CI / ADR / tests / contracts → Git, single source of truth
```

### 1. Knowledge base: MTR tiers

M/T/R are **the same kind of thing** — knowledge assets, tiered by *mastery* and *usage frequency*:

| Store | Full name | Role | Carrier | Write access | Dynamic rule |
|---|---|---|---|---|---|
| **M** | Master | Mastered, verified knowledge and methods | Vector store (read-only) | Transferred in after a human confirms mastery and approves | Frequently used knowledge floats up here |
| **T** | Trial | Drafts, cards, trial records pending verification | Personal Obsidian knowledge base | Agent drafts → artifact store → human approval → controlled write | Default landing zone; floats to M once verified |
| **R** | Rarely | Archive of rarely used T-store material | `_archive/` in the personal knowledge base | Demoted from T; never written directly | Not a graveyard — searchable, reversible, revivable |

> **Dynamic floating**: knowledge moves between tiers by usage. Frequently used → up to M; unused → down to R; R material reused → back up to T or M.

### 2. Project base: P

P is **a different kind of thing** — the project's **physical assets**, not knowledge:

| Store | Full name | Role | Carrier | Write access |
|---|---|---|---|---|
| **P** | Project | Code, config, CI, deploy scripts, project docs (README/ADR), test assets, data contracts | Git repository | Agent produces diffs/proposals only; lands after human review + merge |

**The essential difference**:

| | Knowledge base (M/T/R) | Project base (P) |
|---|---|---|
| Content | "What I know" — concepts, methods, patterns, lessons, decision rationale | "What I built" — code, config, scripts, contracts, tests |
| Form | Markdown cards, notes, docs | Executable files, runnable projects |
| Quality test | Is it trustworthy? Verified? | Does it run? Does it pass tests? Meets contracts? |
| Evolution | Dynamic floating (M ↔ T ↔ R) | Version control (commit/branch/tag) |
| Rollback | Demote/float, never delete | `git revert` / branch reset |
| Source of truth | Vector store (M) + Obsidian (T/R) | Git repository |

### 3. P ↔ T two-way linkage

P and T are not islands — they must be **bidirectionally linked**, and the **knowledge router** is what connects them:

```
P (code / engineering)  ←── reference ──→  T (knowledge / rationale)
     │                                        │
     │  Why is this code written this way?     │  How does this land?
     │  → T-store decision card                │  → concrete implementation in P
     │                                        │
     └──────────── Knowledge Router ───────────┘
                  (read + write route decisions)
```

| Direction | Content | Implementation |
|---|---|---|
| **P → T** (trace rationale) | Decision rationale, trial records, trade-offs behind code | `knowledge_ref: [[T-card]]` in file headers / ADR frontmatter |
| **T → P** (trace implementation) | Where the knowledge actually landed | `impl_ref: p://path/to/file#L10-L30` in card frontmatter |
| **Both directions queryable** | Code → rationale, rationale → code | Router maintains a bidirectional index |

> **Without the router, P and T are two islands** — code has no "why", and the knowledge base has no "where".

---

## I. Mindset layer

### 1. Refresh your sources

> Knowledge expires, models upgrade, best practices iterate. Refresh periodically; don't work from memory.

Four dimensions:

| Dimension | What to refresh | Cadence | Example sources |
|---|---|---|---|
| **World** | Global news, tech trends, major events | Daily / weekly | News aggregators, X/HN trending |
| **Society** | National/local policy, regulation, industry norms | Monthly | Government sites, industry associations |
| **Industry** | New tech, tools, methodologies in your field and role | Monthly | Official changelogs, GitHub releases, practitioner blogs |
| **People** | What people in your circles are building and publishing | Weekly | Follow lists, communities, colleagues |

- **Model behavior changes by version**: Opus 5 self-verifies; newer models are more sensitive to instruction files — six-month-old prompt intuition may be stale
- **Source grading**: tier 1 (official docs / model spec) → tier 2 (practitioner blogs) → tier 3 (community discussion — for spotting trends, not for setting rules)
- **Knowledge base maintenance**: tag notes with `created` and `last_verified`; anything unverified for 6+ months gets `⚠️ needs review` and may be demoted to R
- **Anti-pattern**: treating a successful prompt from six months ago as an eternal template

### 2. Use it or lose it

> Unused skills degrade; unused knowledge sinks. What you don't want to lose, you must keep using.

**A. Information side — MTR dynamic floating**

- Frequently used knowledge/tools/methods → float up to M as reliable assets
- Rarely used T-store material → gradually demotes to R (the archive)
- R material reused → floats back to T or M
- Same for tools and skills: keep frequently used tools at M for fast access; archive unused configs to R without deleting
- **The archive is not a graveyard**: searchable, reversible, revivable — demotion ≠ deletion

**B. Behavior side — keep core skills hands-on** (executed together with principle 3)

- Core skills that require judgment must stay personally involved end-to-end
- Write code without the agent at least once a week — calibrate your real level
- Monthly inventory: which skills have I handed to AI? Which can be safely outsourced? Which must I keep?
- **Cognitive Surrender, three stages**:
  1. Offloading — handed over, but you still understand and can judge ✅ safe
  2. Judgment transfer — AI answers become the default basis ⚠️ dangerous
  3. Surrender — even judgment handed over ❌ out of control
- **Self-test**: write your hypothesis before asking; after the AI answers, name one thing it didn't check
- **Bottom line**: if AI went on strike tomorrow, how long could you last? The answer can't be "zero"

### 3. Delegate tasks, not judgment

> Agents can do the work, but they can't make the decisions. Judgment and taste are the last step you don't outsource. Judgment-heavy core skills must stay hands-on.

- **Safe to delegate**: docs, test coverage checks, mechanical refactors, low-risk migrations
- **Must stay (full personal involvement in design)**: architecture, product decisions ("should we build this"), security/auth/finance, system-permission operations
- **Interrogate hidden decisions** — don't just ask "done?":
  - "What did you decide that I didn't explicitly authorize?"
  - "Where did you rely on assumptions?"
  - "What might this change break elsewhere?"
- **Signature standard**: if you wouldn't sign your name to it, don't ship it
- **Defending against surrender**: you can delegate a lot of *task volume*, but judgment volume cannot scale proportionally

### 21. Credibility weighting (added 2026-11-11)

> Disagreements aren't settled by vote, volume, or rank — they're weighted by **credibility**, and credibility comes from historical evidence, not self-assessment.

**Step 1: draw data from historical experience (the key step)**

Credibility isn't assigned by feel — it's counted from your own knowledge base, **especially the T and R stores**:

| Source | What evidence it provides | How to use it |
|---|---|---|
| **T store** (unverified drafts) | Past attempts at similar problems, failure records, `disputed` tags | If an approach already failed in T → downweight |
| **R store** (archive) | Things once used and later abandoned — **why were they abandoned?** | Heavy R accumulation in a direction → that path doesn't work; downweight |
| **P store** (ADR / git log) | Decisions in force, plus revert history | Highest authority within the project |
| **M store** (verified) | Verified knowledge | High-weight baseline |

> **The R store is an underrated data source**: it records "once active, later abandoned" — a **history of negative samples**. Most people only look at M's successes, never at R's failures, so they walk the same dead end repeatedly.

**Step 2: assign weights**

| Source | Domain | Initial weight | Update rule |
|---|---|---|---|
| Own judgment | Architecture | 0.7 | Verified correct +0.02; wrong −0.05 |
| AI output | Code implementation | 0.8 | −0.08 per reverted PR |
| AI output | Architecture | 0.4 | Heavy cargo-cult history |
| M store entry | General knowledge | 0.9 | Already verified |
| T store entry | General knowledge | 0.5 | Unverified |
| R store entry | Any | 0.3 | Once judged not worth using |
| P store ADR | Project decisions | 0.95 | Highest within project |

**Step 3: resolution rules**

- Weight gap **> 0.3** → higher-weight side wins, but **record the disagreement** (T store, tag `disputed`)
- Weight gap **≤ 0.3** → trigger "thoughtful disagreement": each side writes one thing they're most likely wrong about, then decide
- **Irreversible operation** → regardless of weight, human veto

**Step 4: when you can't resolve it — admit the boundary**

> The easiest step to skip. **When the data can't resolve it either, don't keep arguing — admit "I can't judge this right now."**

Three responses, in priority order:

1. **Shrink the problem** — if the big question can't be decided, split it into decidable pieces
2. **Change the method** — add evidence (experiment, benchmark), change the path, consult an external authority
3. **Admit it can't be done** — record "my current boundary is at X", stop investing, move on

**Why this matters**: credibility weighting has a failure mode — **two low-weight sources, both confident**, insufficient data, neither able to convince the other. Continuing to weight here is dressing up "I don't know" as "I know". **Admitting the boundary is not failure; it's risk control.**

- **Landing zone**: the router's `decision-log` is a natural credibility data source (reverted vs. accepted AI output is countable)
- **Anti-pattern**: treating weighting as a democratic vote — near-equal weights means a 50/50 split, which uses procedure to mask a missing judgment

### 22. Continuous challenge (added 2026-11-11)

> Self-challenge has a ceiling — you systematically route around your own blind spots. You need a **structured external challenger**.

**Two implementations, usable in parallel**

**A. A challenger agent (always available)**

Configure an agent whose job is not to advise but to **find where you can't stand up, at the finest granularity**:

```markdown
# Role: structured challenger

## Sole responsibility
Decompose and challenge at the detail level. Do not offer solutions; only expose problems.

## Every challenge must satisfy (all required)
1. Point to a specific claim / line of code / number — "feels off" is rejected
2. State the basis: quote the source, the data, the code location, or a counterexample
3. State the consequence: "if this challenge holds, what follows?"

## Prohibited
- Vague challenges ("have you considered performance?")
- Rhetorical challenges (questions whose answers you already know)
- Offering solutions (that's a different role)

## Output format
| Claim | Challenge | Basis | Impact if true |
```

**Cadence**: run it before any major decision; weekly otherwise. Treat it as your opposing counsel.

**B. A human challenger (find one)**

- Find people who **challenge you with evidence** — not all opposition is worth hearing, but "evidenced opposition" is extremely scarce
- Meet regularly, one hour, one topic only: "which of my recent judgments is most likely wrong?"
- **Test**: someone who says "you have an implicit assumption Y at point X" is worth 10× someone who says "I don't think this works"

**Why it works**

- Your blind spots are **structural**: you don't even know what you don't know
- Detail-level decomposition breaks the structure — drop from "is this approach good?" to "where does step three's input come from?" and the problem surfaces
- Aligns with principle 5 (trace the chain): challenging isn't rejecting the whole, it's going one level deeper

- **Anti-pattern**: surrounding yourself with people who only say "looks good"; or, once you find a challenger, only defending yourself instead of recording
- **Output**: each challenge's conclusion goes to T store, tagged `challenged`, with the challenger and basis

### 23. Pain is a signal (added 2026-11-11)

> When something is especially hard or painful, don't run. Pain is a **diagnostic signal** reality is sending you — not an enemy to escape.

**Dalio's mechanism (textual basis)**

> Reality will send you strong signals — rewarding you or punishing you — to reflect how well your principles are working. That way you can learn to adjust those principles accordingly.
> — *Principles*, life principle #1, "Embrace reality and deal with it"

> **Pain + Reflection = Progress**

**The full chain**

```
Pain appears
  ↓ ① Stop. Don't flee, suppress, or react immediately
Identify the signal
  ↓ ② Which kind of pain is this?
Locate the lesion
  ↓ ③ What weakness / blind spot / false assumption does it expose?
Reflect
  ↓ ④ Write down: what happened / what am I avoiding / what's my responsibility
Extract a principle
  ↓ ⑤ "Next time X happens, I won't Y; I'll first Z"
Act
  ↓ ⑥ Small step, verify, review
Progress + principle library upgraded
```

**② Four sources of pain (classify before treating)**

| Type | Signal | Response |
|---|---|---|
| **Boundary of competence** | Can't do it; many attempts, still stuck | Admit the boundary → change method or stop (see principle 21, step 4) |
| **Cognitive blind spot** | Thought you understood; you didn't | Back to principle 5A; trace until even the AI goes vague |
| **False assumption** | The premise itself is wrong | Back to principle 4; rewrite the spec |
| **Uncontrollable** | External factor you can't change | Accept; stop spending cognitive resources |

**③ Six questions for reflection**

1. What happened? (facts, no evaluation)
2. What exactly is the pain point? (which step)
3. What am I avoiding? (the thing you don't want to face)
4. What weakness, blind spot, or false assumption does this expose?
5. What's my responsibility, and what's external fact?
6. What can I control, and what can I change next?

**④ The reusable-principle template**

```
"Next time ______ happens, I won't ______; I'll first ______, then ______."
```

Write it to T store, tagged `from_pain`. **This is the only thing pain can be exchanged for — a principle you can use next time.**

**On "the two yous" and meditation**

*Principles* also contains a "lower-level self vs. higher-level self" framing and recommendations for meditation (the lower self seeks comfort and approval; the higher self seeks truth and growth; meditation creates distance from emotion). **These specific passages were not retrieved as verbatim chunks during research for this document — treat as a pointer only, and verify against the source before quoting.**

**Three bottom lines**

- Don't use distraction, complaining, or blame to kill the pain — that silences the signal while the problem remains
- Don't suppress and don't romanticize — allow the feeling, but don't let it drive decisions
- Separate "fixable" from "uncontrollable" — act on the former, accept the latter

- **Anti-pattern**: reaching for a technical fix first and skipping "why am I in pain" — you treat the symptom, not the lesion
- **Alignment**: this is the in-the-moment version of principle 10 (deep retrospective) under emotional load. Retrospective is after; this is during.

---

## II. Discipline layer

### 4. Spec first

> A spec is not a prompt. A spec is product thinking made explicit. A vague spec multiplies errors across dozens of parallel runs.

- **Write a requirements note before prompting**: who it's for, what problem it solves, what not to do, what counts as done
- **A spec must contain**: expected outcome, constraints, acceptance criteria, files/interfaces that must not be touched
- **Watch for Intent Debt**: unclear goals and trade-offs get decided for you
- **Two-round rule**: if two rounds of prompt adjustment don't get you there, revisit the requirements — don't keep stacking prompts
- **Plan mode first**: align on approach before writing code; "if you could describe the diff in one sentence, skip the plan"
- **Explore → Plan → Implement → Commit** (Anthropic's recommended rhythm)

### 5. Trace the chain

> Dig immediately; leave no blind spots. Running ≠ correct. Ask until you reach the edge where even the AI goes vague.

**A. Information side — trace a concept to its boundary**

- When a concept warrants depth, ask until the AI starts hedging and can no longer give a confident answer — **that edge is where you must supply your own evidence**
- **Example**: "What is RAG?" → clear answer → "What's the essential difference from fine-tuning?" → clear → "When is RAG worse than fine-tuning?" → the AI starts equivocating → **that's the boundary**; you need experiments or papers
- **A green check proves one thing only**: what did it test? What didn't it test? Look at what ran and what it returned before reading the summary
- **Linear walkthrough**: for an unfamiliar codebase, trace the execution path from the entry point, not random browsing
- **Comprehension Debt**: every blind approval is a small loan that compounds

**B. Behavior side — dig into engineering trade-offs**

- Control the key trade-off and risk points; dig deep into the affected chains
- Use AI to explore repeatedly and derive an experimentally validated **sample space** plus **success/failure chains**
- This is how you gain command of risk — not "the AI said so", but "I tried N paths; I know which work, which don't, and why"
- **Debug beyond symptoms**: agent gives a fix → ask "why does this fix work? what's the root cause?" — otherwise a related symptom returns in two weeks
- **Design decisions must be reconstructable**: if you can't rebuild "why we did this", you inherited the model's confidence without its reasoning

### 6. Ask immediately when you don't understand (linked with principle 5)

> The moment you hit ambiguity, find the source or ask the AI (knowledge base retrieval), then trace it to the bottom per principle 5.

- **Default agent behavior**: the OpenAI Model Spec has models ask for clarification on ambiguity rather than acting — humans should too
- **Cost of ambiguity**: 30 seconds clarifying at spec time beats 30 minutes correcting at review time
- **Where to ask**:
  - Requirement ambiguity → ask the requester
  - Concept unclear → ask the agent to explain (conceptual inquiry > generation), then trace per 5A
  - Tool behavior uncertain → read the docs, don't guess
  - Knowledge base may have it → check M (vector store), then T (Obsidian)
- **Anti-pattern**: "let's just run it and see" — using agent output to reverse-engineer requirements

### 7. Connect to the system

> Knowledge isn't isolated; experience must sediment. Every pattern and landmine goes back into MTR-P. All Q&A gets archived as first-hand corpus for a future digital twin.

- **Write back to the knowledge base (MTR)**: experience, patterns, landmines, decisions → T store (draft) → M store after approval
- **Write back to the project base (P)**: code, config, contracts land after diff + review; **never auto commit/push/merge**
- **P ↔ T bidirectional linkage (critical)**:
  - **P → T**: every important code block's "why" must trace to a T-store card (rationale, trial record, trade-off)
  - **T → P**: every actionable card must point to its implementation (`impl_ref: p://path/to/file#Lx-Ly`)
  - **Unlinked is tech debt**: code without rationale means the next person (or AI) can't judge whether to change it
- **Archive the Q&A**: valuable exchanges with agents go into T-store cards — first-hand corpus for the digital twin
- **Obsidian bidirectional links**: new notes must link to existing ones; orphan notes are sunk knowledge
- **The router is mandatory**: routing between P and T is decided by the knowledge router, not human memory (see chapter VII)
- **AGENTS.md / CLAUDE.md compounding**: anything you corrected twice goes into the instruction file
- **Four-store write-back chain**:

```text
New information appears
  ↓
Knowledge router decides (kind + source + confidence)
  ├─ Project physical asset → P (proposal → review → merge)
  ├─ Project decision rationale → P (ADR) + T (card, dual write)
  ├─ General method/pattern → T (status: unverified)
  ├─ Verified knowledge → M (human confirmation + approval)
  └─ Low-frequency content → R (auto demotion)
```

- **Digital twin goal**: Q&A, decisions, trials, success/failure chains all archived *and* bidirectionally linked to project artifacts
- **Anti-pattern**: experience stays in chat logs and dies when the session ends; code and knowledge base never reference each other

---

## III. Practice layer

### 8. Parallel thinking

> Parallelism isn't about speed; it's about approaching truth from multiple directions. But it requires **having your own hypothesis before you hand the task over**.

**Core meaning** — before handing a task to AI, you must:

1. **Already have a hypothesis/design**: don't hand over a blank page
2. **Know your existing tools**: which reusable tools, methods, patterns exist in M/T/R
3. **Have a complete verification chain**: know how you'll validate the AI's output
4. **Let AI supplement your information and toolset**: its role is filling gaps you didn't anticipate, not replacing your thinking
5. **Let AI contribute thinking during execution**: insights generated mid-run collide with your initial design
6. **Produce a combined result**: human + AI thinking merged, not raw AI output

**Operating rules**:

- **WIP limit**: 3–5 concurrent agents is the sweet spot; beyond that, review capacity fails
- **Orchestration Tax**: attention cost rises sharply past the first few agents
- **One file, one owner**: two agents editing the same file is an overwrite waiting to happen
- **Parallel vs. serial**:
  - Parallel: independent research, analysis, docs, security review, changes to different modules
  - Serial: dependencies exist, requirements still being explored, overlapping files
- **Multi-model routing**: planning/architecture → cheaper model; implementation → mid-tier; review → dedicated model
- **Kill criteria**: agent stuck on the same error 3+ iterations → stop and reassign

### 9. Deploy fast (linked with principle 6)

> Verify fast, deploy fast, roll back faster. When learning a new concept or demo, build it and deploy it successfully, then review the success chain for reusable architecture.

**Two layers**:

**A. Learning a new concept / demo-level project**

- Encounter a new concept or demo → build it immediately and get it deployed
- After deployment works → review the success chain
- Extract reusable architecture / structure / implementation details from that chain
- Software projects mostly have this capability now; hardware is worth watching as the tooling matures
- **Linked with principle 6**: ask immediately (6) → then do it immediately (9) → then review the chain

**B. Day-to-day engineering**

- **Verification is the bottleneck, not generation**: agents produce fast; confirming correctness is slow — automate verification
- **Verification as a hard exit**: every agent-completed task ends in concrete evidence — test run, screenshot, log, reviewer sign-off
- **Red/green TDD**: failing test first → agent implements → verify
- **Two failed corrections → reset**: clear context, rewrite the prompt; don't stack prompts on a rotten context
- **Small PRs**: 50 lines is readable, 600 is not. Review unit = comprehension unit
- **Cheap rollback**: git worktree isolation + `/rewind` make code and conversation rollback cheap
- **Anti-pattern**: firing off a 600-line PR — what you approved isn't code, it's an abdication of code

### 10. Deep retrospective

> Iteration without retrospective is just repetition. After any significant work, update the system. The core output of a retrospective is a **trusted chain**.

Six-step production line:

```
Plan → Spawn → Monitor → Verify → Integrate → Retro
```

**Retrospective checklist**:

| Dimension | Question |
|---|---|
| **What was missed** | Which risks/dependencies/boundaries were missed at planning? Why? |
| **Over-engineering** | What was built too complex? What could be simpler? |
| **Unclear standards** | Which acceptance criteria were vague or incomplete? What did that cause? |
| **Unsatisfactory results** | Which outputs fell short? Root cause? |
| **Why it went badly** | Unclear spec? Bad judgment? Insufficient verification? Runaway parallelism? |
| **What went well** | Which practices worked? **How do we turn them into reusable components? — this is the key to a trusted chain** |

**Building the trusted chain**:

- What worked → distilled into reusable components → T store → M store after verification
- **Trusted chain = an empirically validated, reusable success path** — from spec → execution → verification → deployment
- The trusted chain is the digital twin's core asset: agents can later execute from it instead of starting from zero
- **REFLECTION.md**: after each task, force the agent to write a reflection — what surprised us? one pattern for AGENTS.md? one prompt improvement?
- **Have the agent quiz you**: after a large change, have it summarize and generate questions — pass before merging

---

## IV. Guardrails layer

### 11. Layered rules

> Not all rules are equal. The request layer can flex; the enforcement layer must be iron.

| Tier | Nature | Examples |
|---|---|---|
| **Request layer** (agent may weigh) | Soft constraint | prompts, CLAUDE.md, skills, T-store notes |
| **Enforcement layer** (always applied) | Hard constraint | hooks, deny rules, CI checks, managed settings, deterministic gates |

- **Drop the old habits**: ALL CAPS, "think harder", emphasis in prompts to force compliance — use a hook instead
- **Enforce hard rules with tooling**: block turn completion until tests pass; deny writes to sensitive paths; CI gates that block eval regressions
- **Agent may propose the next state; only the control plane may change it**
- **Deterministic gates outrank model assessment**: model judgment must never override identity, path, schema, budget, version, or test gates

### 12. Detect and observe

> A change isn't complete until the system can observe whether it produced the intended effect.

- **Detect and Deliver must move together**: agents accelerate delivery, but if detection doesn't keep pace, the gap between "what changed" and "what we know about what changed" widens
- **A change isn't done until observable**: not complete until the system can observe its effect
- **Three linked outputs**: the fix itself + a test that reproduces the defect + an instruction-file update preventing recurrence
- **Dual-status display**: `execution_status` and `business_status` are shown separately and understood separately — never inferred from each other
- **Metrics**: PR cycle time, rework, reverts — **not** "how much code AI wrote"
- **Anti-pattern**: agent produces → human approves → ships → the customer finds the problem first

### 13. Least privilege

> Implementation autonomy can be high; operational authority must be low.

- **Agent may do autonomously**: search the repo, create branches, edit approved paths, run local tests, generate tests, fix failing unit tests, prepare PRs
- **Agent must not do automatically**: disable security checks, change the definition of success, lower quality thresholds, approve its own code, access production data, merge high-risk changes, deploy to production
- **Four-store permission boundaries**:
  - M (vector store, knowledge) → read-only retrieval; never auto-write
  - T (Obsidian, knowledge) → agent drafts; controlled write after artifact store + human approval
  - R (Obsidian archive, knowledge) → demoted from T; never written directly
  - P (Git, project) → diffs/proposals only; **never auto commit/push/merge**
- **The router cannot overreach**: it decides routes and produces proposals only; it never writes to any store — writes go through each store's approval channel
- **Isolated execution**: git worktree / cloud sandbox
- **Human-on-the-loop**: agent runs Plan-Implement-Build-Test-Correct; the human sets goals, watches for drift, handles exceptions, owns the quality bar

### 14. Reversibility

> Every operation must be reversible. Irreversible operations require extra approval. Rollback is a safety net, not a failure.

- **Code is reversible**: worktree isolation + branch strategy + `/rewind`
- **Knowledge is reversible**: MTR dynamic floating — demotion to R is not deletion; searchable and revivable
- **Methods are reversible**: rolling back a method trial only re-points the version reference; failed trials, evaluations, and history are never deleted or overwritten
- **Approvals are reversible**: if content changes by even one byte after approval, the approval is void — re-stage and re-approve
- **State is recoverable**: after a control-plane restart, tickets, attempts, approvals, outbox, and business receipts must not be lost
- **Irreversible operation list** (extra approval required): deleting production data; publishing/messaging externally; modifying global config; online deployment; `git push --force`
- **Anti-pattern**: executing with no rollback path — when it breaks, there's no way back

---

## V. Knowledge base layer

### 15. Layered retrieval

> Retrieval has priorities; don't query everything blindly. Inside a project context, P first. For general knowledge, M first. R is the archive — query it only when explicitly triggered.

**Two priority sets, switched by scenario**:

| Scenario | Order | Rationale |
|---|---|---|
| **Project question** ("how does this project do X") | **P → M → T → R** | Project-specific conventions outrank general knowledge |
| **General knowledge** ("what is X") | **M → T → R** (P only as examples) | Verified beats unverified |
| **Method/skill lookup** | **M → T → R** | Mastered methods first |
| **Decision history** | **P (ADR) → T (decision cards) → M** | Decisions are dual-written; start from the project side |
| **Obscure detail** | **R (explicit trigger)** | The archive isn't offered proactively |

**Per-store notes**:

- **P first (in project context)**: "why does this project use SQLite WAL?" → check P's ADR, then T's decision card — not the AI's general knowledge
- **M first (general knowledge)**: query the vector store for verified knowledge
- **T second**: when M lacks it, query Obsidian; watch the `status: unverified` flag
- **R archive**: searchable but never offered; using it triggers the float-up mechanism
- **Promotion trigger**: R material retrieved and used → marked active → floats to T → floats to M after verification
- **Demotion trigger**: T material unused/unverified beyond the configured period → demoted to R
- **M write constraint**: only via human mastery confirmation + approval + controlled adapter; the agent never writes M
- **Anti-pattern**: skipping P and M to ask the AI — ignoring verified knowledge and project decisions in favor of a possibly hallucinated answer

### 16. Concept inquiry before generation

> When learning something new, have the agent explain before having it generate.

- **Anthropic skill-formation research**: engineers who used AI to *generate* code while learning a new library scored **17% lower** on a follow-up comprehension quiz; those who used AI for *conceptual inquiry* held their ground
- **Same tool, different posture, different outcome**: asking builds the mental model; generating erodes it
- **Practice**: new library/framework → check whether M already has mastered knowledge → if not, have the agent explain architecture and trade-offs → form your own judgment → then let it write code
- **Linked with 5A**: during concept inquiry, trace until the AI goes vague

### 17. Human curation

> AGENTS.md / CLAUDE.md / core knowledge-base content can only be written by humans, never auto-written by agents.

- **ETH Zurich research**: LLM-generated AGENTS.md offers no benefit and can marginally reduce success rates (~3%) while increasing inference cost by 20%+; human-written files provide a ~4% improvement
- **Lead approves every line**: the agent may propose entries; a human must review and merge
- **MTR-P mapping**:
  - M store → transferred in after human mastery confirmation + approval; agent never auto-writes
  - T store → agent generates `status: unverified` drafts; controlled write after human approval
  - AGENTS.md → agent may propose; human must merge
- **Same for the knowledge base**: core notes in Obsidian / the vector store must be human-curated; agent output is draft only

### 18. Evidence retention

> In multi-agent relay, information thins out. Critical tasks must retain source material and verification evidence.

- **Agent A's summary becomes Agent B's input → key details may already be lost**
- **Keep the raw material**: requirements, design decision records, test output, review comments — not just the final summary
- **Auditable chain**: every decision has a source; every change has a diff + test result + review record
- **Artifact store**: all draft byte streams are stored by SHA-256, tamper-evident
- **Anti-pattern**: approving on the strength of an agent's final summary without tracing back to evidence

### 19. Knowledge routing (added 2026-11-11)

> P and T require a routing middleware between them. Which route to read, which store to write, cannot depend on human memory — it must be decided by deterministic rules and be auditable.

- **The router is the required connector between P and T**: without it, they are two islands
- **Both directions need routing**:
  - **Read path**: a question arrives → decide which stores, in what order, at what weight, when to degrade
  - **Write path**: new information appears → decide which store, what status, which approval
- **Routing must be deterministic rules, not free-form LLM judgment**: aligns with "deterministic gates outrank model assessment" — rules live in config files that can be reviewed, tested, and versioned
- **Routing must be auditable**: every decision logs what was queried, what hit, why it degraded — so you can trace failures
- **The router cannot overreach**: it decides routes and produces proposals only; it never writes to any store
- **Bidirectional linkage needs an index**: `knowledge_ref` (P → T) and `impl_ref` (T → P) require a reverse-lookup index maintained by the middleware
- **Anti-pattern**: relying on the human brain to remember which store has what; letting the agent decide freely each time — the same question returns different answers on two runs

---

## VI. Expression layer

### 20. Maximum concision

> Use the fewest words to say the most accurate thing. Concision isn't omission — it's density.

- **Spec/prompt concision**: if a spec can't state its core intent within a page, you haven't thought it through
- **Card concision**: MTR-P cards contain only key facts, evidence, and boundaries — no filler, no restating the known
- **Instruction file concision**: too-long AGENTS.md / CLAUDE.md gets half-ignored (Anthropic explicitly warns about this)
- **Communication concision**: with agents, be precise to the point; avoid vague modifiers
- **Judgment density**: every sentence carries information — "the code runs" isn't information; "the code fails under condition Y in scenario X" is
- **Anti-pattern**: a 10,000-word spec where the single core requirement is never stated clearly

---

## VII. Knowledge router design

> Corresponds to principle 19. This is a design document, not implemented capability — validate each item against your own gates before building.

### 7.1 Goals and boundaries

**Goal**: establish a **deterministic, auditable, testable** routing layer between P and MTR, solving three problems:

| Problem | Today's pain | What the router does |
|---|---|---|
| **Where to read** | Relying on memory for "which store has this"; agents decide freely, results unstable | Rule-based read plans; the same question hits the same stores |
| **Where to write** | New lessons get scattered across chat logs, Obsidian, code comments | Route by content type + confidence → target store and status |
| **Broken linkage** | Code has no "why"; knowledge has no "where" | Maintains `knowledge_ref` ↔ `impl_ref` bidirectional index |

**Boundaries (what it does NOT do)**:

- ❌ Implement retrieval itself — M uses the vector store, T/R use Obsidian full-text search, P uses ripgrep/git grep
- ❌ Write to any store — it outputs read plans and write proposals only; writes go through each store's approval channel
- ❌ Make routing decisions with an LLM — an LLM may optionally classify query intent, but the result must map to an enum, never free-text routing
- ❌ Replace the control plane — it is a **submodule** of it, sharing tickets/approvals/artifacts

### 7.2 Architecture position

```text
                    User / Agent question
                             │
                    ┌────────▼────────┐
                    │  Control Plane  │
                    │  ┌───────────┐  │
                    │  │ Knowledge │  │  ← this design
                    │  │  Router   │  │
                    │  └─────┬─────┘  │
                    └────────┼────────┘
                             │
          ┌──────────────────┼──────────────────┐
     ┌────▼────┐        ┌────▼────┐        ┌────▼────┐
     │  Read   │        │  Write  │        │  Index  │
     │  Path   │        │  Path   │        │  Sync   │
     └────┬────┘        └────┬────┘        └────┬────┘
          │                  │                  │
     ┌────▼──────────────────▼──────────────────▼────┐
     │  Adapters (each store's own, no overreach)     │
     │  Vector(M) │ Obsidian(T/R) │ Git(P) │ Artifact │
     └───────────────────────────────────────────────┘
```

**Key constraint**: the router outputs **plans** and **proposals** only; adapters execute, the control plane approves.

### 7.3 Read path

#### 7.3.1 Inputs

| Field | Type | Notes |
|---|---|---|
| `query` | string | The question |
| `intent` | enum | `project_context` / `general_knowledge` / `method_skill` / `decision_history` / `cold_detail` |
| `project_id` | string? | Required when project context applies |
| `caller` | string | Who's asking |
| `budget_ms` | int | Total time budget, used to decide degradation |
| `explicit_stores` | string[]? | Caller explicitly names stores (e.g. "check the archive") |

#### 7.3.2 Routing table (deterministic)

| intent | Plan (order) | Weights | Degrade condition | Fallback |
|---|---|---|---|---|
| `project_context` | **P → M → T** | 1.0 / 0.8 / 0.6 | P hits below threshold or unreachable | R (explicit) |
| `general_knowledge` | **M → T** | 1.0 / 0.7 | M hits below threshold | P (as examples), R (explicit) |
| `method_skill` | **M → T** | 1.0 / 0.8 | M empty | R (explicit) |
| `decision_history` | **P(ADR) → T → M** | 1.0 / 0.9 / 0.5 | No ADR in P | — |
| `cold_detail` | **R** | 1.0 | R empty | T |

**Weight semantics**: used for merge ranking (`final_score = similarity × weight`), not for filtering.

**Degrade triggers (any one)**:

1. Primary store returns zero results
2. Primary store's top similarity < `min_relevance` (default 0.55, configurable)
3. Primary store exceeds `budget_ms × 0.6`

**Prohibitions**:

- ❌ Fleet-wide parallel scan by default — violates principle 15's layering
- ❌ R participating in default retrieval — the archive is only enabled for `intent=cold_detail` or explicit request
- ❌ Letting the agent assemble "query M and P and T" itself — callers supply intent only; rules decide the route

#### 7.3.3 Output

```json
{
  "route_id": "rt_20261111_150210_ab12",
  "intent": "project_context",
  "plan": [
    { "store": "P", "adapter": "git-grep", "filter": { "project_id": "example-project" }, "weight": 1.0, "top_k": 8 },
    { "store": "M", "adapter": "vector-db", "weight": 0.8, "top_k": 5 },
    { "store": "T", "adapter": "obsidian-search", "weight": 0.6, "top_k": 5 }
  ],
  "degrade_policy": { "min_relevance": 0.55, "on_empty": "next" },
  "fallback": { "store": "R", "trigger": "explicit_only" },
  "created_at": "2026-11-11T15:02:10+08:00"
}
```

#### 7.3.4 Interface

```http
POST /v1/knowledge/route/query

{
  "query": "why does this project use SQLite WAL instead of PostgreSQL",
  "intent": "decision_history",
  "project_id": "example-project",
  "caller": "workbuddy",
  "budget_ms": 2000
}
```

### 7.4 Write path

#### 7.4.1 Inputs

| Field | Type | Notes |
|---|---|---|
| `content` | string | What to write back |
| `kind` | enum | `code_change` / `config` / `adr` / `method` / `lesson` / `qa_archive` / `doc` |
| `source` | object | `{agent_id, task_id, attempt_id, trace_ref}` |
| `evidence` | object | `{tests, screenshots, logs, sha256}` |
| `confidence` | enum | `verified` (has test/measurement evidence) / `unverified` (reasoning only) |
| `project_id` | string? | Required when project-related |

#### 7.4.2 Routing table

| kind | confidence | Target | Status | Approval |
|---|---|---|---|---|
| `code_change` | any | **P** (diff proposal) | proposal | human review + merge |
| `config` | any | **P** (diff proposal) | proposal | human review + merge |
| `adr` | any | **P (ADR file) + T (decision card), dual write** | proposal / unverified | human approval |
| `method` | verified | **M** (+ T for the record) | verified | human mastery confirmation + approval |
| `method` | unverified | **T** | unverified | human curation |
| `lesson` | any | **T** (→ linked to P via `knowledge_ref`) | unverified | human curation |
| `qa_archive` | any | **T** (Q&A archive section) | draft | human curation (batchable) |
| `doc` | any | **P** (project docs) or **T** (general docs) | proposal / unverified | store-specific approval |

**Demotion rule**: T entries not retrieved within `stale_days` (default 180) → propose demotion to R (as a proposal, never automatic).

**Promotion rule**: R entries retrieved ≥ `hit_threshold` (default 2) times within `window_days` (default 30) → propose promotion to T.

#### 7.4.3 State machine

```text
classify (deterministic rule)
   ↓
route (pick target store + status)
   ↓
stage (write content to artifact store, compute SHA-256)
   ↓
propose (target path + hash + rationale)
   ↓
approve (human; binds approval_id + base_version + artifact_sha256)
   ↓
commit (adapter performs controlled write)
   ↓
index (update bidirectional index + decision log)
   ↓
   failure at any step → keep the artifact, mark failed, no partial write
```

#### 7.4.4 Interface

```http
POST /v1/knowledge/route/write

{
  "kind": "method",
  "content": "...# MTR dynamic floating rules...",
  "source": { "agent_id": "card-agent", "task_id": "T07", "attempt_id": "a_003" },
  "evidence": { "sha256": "sha256:...", "tests": ["evals/method_trial_01.json"] },
  "confidence": "unverified",
  "project_id": "example-project"
}
```

Response:

```json
{
  "route_id": "rt_...",
  "targets": [
    { "store": "T", "path": "2-Permanent/Resources/_methods/mtr-floating.md", "status": "unverified" }
  ],
  "proposal_id": "prop_09f2",
  "approval_required": true,
  "reason": "kind=method & confidence=unverified → T store, unverified"
}
```

### 7.5 Bidirectional index

#### 7.5.1 Two reference fields

| Field | Written in | Format | Purpose |
|---|---|---|---|
| `knowledge_ref` | P files (comment header / ADR frontmatter) | `[[T-card-name]]` or `m://vector-db/doc_id` | Trace "why" from code |
| `impl_ref` | T card frontmatter | `p://path/to/file#L10-L30` | Trace "where" from knowledge |

#### 7.5.2 P-side example

```python
# example-project/src/db.py
# knowledge_ref: [[card-sqlite-wal-choice]] m://vector-db/adr-db-001
# reason: single-host low-concurrency scenario, WAL suffices; migrate to PostgreSQL on multi-host (see ADR-0001)
ENGINE = create_engine("sqlite:///control.db", connect_args={"journal_mode": "WAL"})
```

#### 7.5.3 T-side example

```yaml
---
title: card-sqlite-wal-choice
type: decision
status: unverified
project_ref: p://example-project
impl_ref:
  - p://example-project/src/db.py#L1-L8
  - p://example-project/docs/adr/0001-db-choice.md#L20-L45
---
```

#### 7.5.4 Index maintenance

- **Sync on write**: the router updates the index after `commit`
- **Periodic validation**: daily sweep; if an `impl_ref` target file or line range has changed → mark `stale_ref` (never auto-fix; prompt a human)
- **Orphan detection**: P has a `knowledge_ref` but no matching T card → `dangling_knowledge_ref`; T has an `impl_ref` but the file is gone → `dangling_impl_ref`

### 7.6 Rule configuration (versionable)

```yaml
# config/knowledge-router/rules.yaml
version: 1
read:
  intents:
    project_context:
      plan: [P, M, T]
      weights: { P: 1.0, M: 0.8, T: 0.6 }
      fallback: [R]
    general_knowledge:
      plan: [M, T]
      weights: { M: 1.0, T: 0.7 }
      fallback: [P_example, R]
    decision_history:
      plan: [P_adr, T, M]
      weights: { P_adr: 1.0, T: 0.9, M: 0.5 }
    method_skill:
      plan: [M, T]
      weights: { M: 1.0, T: 0.8 }
    cold_detail:
      plan: [R]
      weights: { R: 1.0 }
  degrade:
    min_relevance: 0.55
    time_ratio: 0.6
write:
  routes:
    - match: { kind: [code_change, config] }
      target: P
      mode: diff_proposal
      approval: human_review
    - match: { kind: adr }
      target: [P, T]
      mode: dual_write_proposal
      approval: human_review
    - match: { kind: method, confidence: verified }
      target: M
      mode: proposal
      approval: human_confirm_mastery
    - match: { kind: method, confidence: unverified }
      target: T
      status: unverified
      approval: human_curation
    - match: { kind: [lesson, qa_archive, doc] }
      target: T
      status: draft
      approval: human_curation
  lifecycle:
    stale_days: 180
    revive_window_days: 30
    revive_hit_threshold: 2
audit:
  log_path: EVIDENCE_ROOT/knowledge-router/decision-log.jsonl
  retain_days: 365
```

**Why config over code**: routing rules change (new stores, adjusted weights, new intents). Config can be reviewed, diffed, and rolled back — satisfying principle 11 (layered rules) and principle 14 (reversibility).

### 7.7 Audit log

Every routing decision writes one JSONL line:

```json
{
  "route_id": "rt_20261111_150210_ab12",
  "ts": "2026-11-11T15:02:10.412+08:00",
  "direction": "read",
  "intent": "decision_history",
  "caller": "workbuddy",
  "project_id": "example-project",
  "plan": ["P_adr", "T", "M"],
  "hits": { "P_adr": 2, "T": 1, "M": 0 },
  "degraded": false,
  "top_score": 0.91,
  "elapsed_ms": 412,
  "rules_version": 1
}
```

**Three uses**:

1. Post-mortem "why didn't we find it" — inspect plan and hits
2. Tune `min_relevance` and weights against real hit distributions
3. Satisfy principle 12 (observability) and principle 18 (evidence retention)

### 7.8 Alignment with existing boundaries

| Existing constraint | How the router complies |
|---|---|
| M store starts read-only | The M adapter implements search only, never write |
| T store writes are controlled | Writes go through artifact store → proposal → approval |
| R store is never written directly | Accepts demotion proposals from T only |
| P store never auto-commits | Produces diff proposals; never commits or pushes |
| Deterministic gates outrank model assessment | Routing rules are config; the LLM never makes the primary decision |
| Attempts must be reproducible | Decision log records `rules_version`; replayable |
| External actions default off | The router has no outbound capability |

### 7.9 Minimum viable implementation

| Stage | Scope | Deliverable | Acceptance |
|---|---|---|---|
| **MVI-0** | Read path only, read-only | `route/query` + rule config + decision log | Same question hits the same stores 3 times; R never appears |
| **MVI-1** | Add write path, proposals only | `route/write` + proposal + artifact staging | Each kind lands in the right store; nothing written without approval |
| **MVI-2** | Add index and orphan detection | Index table + daily sweep | Dangling references reported; stale refs located |
| **MVI-3** | Add lifecycle (promote/demote) | Demotion/promotion proposals | 180-day-unused produces a demotion proposal; 2 hits produce a promotion proposal |

> **MVI-0 alone is already useful** — most of the pain (unstable retrieval) is solved there. Write routing and indexing come second.

---

## Quick reference

| # | Principle | One line | Layer | Links |
|---|---|---|---|---|
| 1 | Refresh your sources | World / society / industry / people, four dimensions | Mindset | MTR upkeep |
| 2 | Use it or lose it | MTR floating + keep core skills hands-on | Mindset | → 3 |
| 3 | Delegate tasks, not judgment | Work can be outsourced; judgment can't | Mindset | ← 2, → 8 |
| 4 | Spec first | Think it through first; ambiguity multiplies | Discipline | — |
| 5 | Trace the chain | Concepts to the boundary; engineering into trade-offs | Discipline | → 6 |
| 6 | Ask immediately | Hit ambiguity, look it up at once | Discipline | ← 5, → 9 |
| 7 | Connect to the system | Write back; archive Q&A for the digital twin | Discipline | MTR |
| 8 | Parallel thinking | Have a hypothesis, tools, and a verification chain first | Practice | ← 2B, ← 3 |
| 9 | Deploy fast | Build and deploy new concepts; review the success chain | Practice | ← 6 |
| 10 | Deep retrospective | Build a trusted chain out of every outcome | Practice | → 7 |
| 11 | Layered rules | Hard rules via hooks; deterministic gates first | Guardrails | — |
| 12 | Detect and observe | Done only when observable; statuses kept separate | Guardrails | — |
| 13 | Least privilege | High implementation autonomy, low operational authority | Guardrails | — |
| 14 | Reversibility | Everything reversible; irreversible needs approval | Guardrails | MTR floating |
| 15 | Layered retrieval | Project→P, general→M, R only on request | Knowledge | four stores |
| 16 | Concept before generation | Ask before you generate | Knowledge | → 5A |
| 17 | Human curation | Instruction files and core notes are human-written | Knowledge | four stores |
| 18 | Evidence retention | Keep raw material across agent relays | Knowledge | artifacts |
| 19 | Knowledge routing | P↔T via deterministic, auditable middleware | Knowledge | → 7, 15 |
| 20 | Maximum concision | Fewest words, most accurate meaning | Expression | — |
| 21 | Credibility weighting | Weight by history; admit the boundary when undecidable | Mindset | ← T/R stores, → 23 |
| 22 | Continuous challenge | A structured external challenger breaks structural blind spots | Mindset | → 5 |
| 23 | Pain is a signal | Pain + reflection = progress; classify before treating | Mindset | ← 21, → 10 |

---

## Sources and credits

| Source | Contribution | Principles |
|---|---|---|
| **Addy Osmani** (Anthropic) | Cognitive surrender, comprehension debt, WIP limits, factory model, delegate-not-judge | 1, 2, 3, 4, 8, 9, 10 |
| **Simon Willison** | Agentic Engineering Patterns, TDD with agents, hoarding reusable solutions | 2, 4, 7, 9, 16 |
| **GitHub Blog** | Typed schemas, action schemas, MCP enforcement, Detect/Deliver parity | 5, 11, 12, 13 |
| **Agent Standard** (Anthropic + OpenAI + Cognition + LangChain) | Determinism by default, architecture over framework, harness > model, eval-driven, security as structure | 4, 11, 12, 13 |
| **OpenAI Model Spec** | Instruction hierarchy, clarify on ambiguity, prefer reversible actions | 6, 13, 14 |
| **Anthropic Constitution** | Four-tier priority: safety > ethics > compliance > helpfulness | 11, 13 |
| **ETH Zurich research** | LLM-generated AGENTS.md offers no benefit; human curation helps | 17 |
| **Anthropic skill-formation paper** | Generation vs. conceptual inquiry (−17% comprehension) | 16 |
| **Ray Dalio, *Principles*** | Pain + reflection = progress, idea meritocracy, five-step process, machine model, encoding principles | 10, 11, 12, 18, 21, 22, 23 |
| **Internal engineering practice** | Dual-status separation, deterministic gates, approval binding, MTR-P store design | 7, 11, 12, 13, 14, 15, 17, 18 |
| **Asix.Lee (元波), original drafts** | Four-dimension source refresh, MTR floating, redfined parallel thinking, dual-track chain tracing, fast deployment for learning, trusted chain, credibility data from T/R stores, structured challengers, pain classification, MTR-P naming, router design | 1, 2, 5, 8, 9, 10, 14, 15, 19, 21, 22, 23 |

---

## Key takeaways

> **MTR-P**'s core isn't "how to use agents" — it's "how to keep judgment and control over your own system while agents do much of the work."
>
> **`MTR` is "what I know"; `P` is "what I built."** The two have entirely different quality tests, evolution mechanisms, and rollback methods. Separating them with a hyphen is the system's first principle.
>
> **MTR dynamic floating** is the use-it-or-lose-it mechanism for knowledge assets — frequently used floats to M, unused sinks to R, reuse revives it. Like memory: what you use sticks, what you don't fades, but nothing is permanently lost.
>
> **The knowledge router is the connector between P and T.** Without it, code has no "why" and knowledge has no "where" — two islands drifting apart. And routing must be deterministic, auditable, and non-overreaching, never left to the agent's discretion each time.
>
> **A trusted chain** is the core output of a retrospective — not "this got done," but "this approach was validated and can be reused as a component, so an agent can later execute from it."
>
> **Parallel thinking** isn't "run several agents." It's "have a hypothesis + baseline tools + a verification chain before handing it over; let AI supplement, not replace."
>
> **Maximum concision** is about density — not omitting information, but making every sentence carry some.
>
> **Credibility weighting**'s hard part isn't the weighting, it's the data sourcing: your T and R stores *are* the historical database. The R store is especially underrated — it's a history of negative samples. Look only at M's successes and you'll repeat the same dead end. And the most honest endpoint of weighting is **admitting you can't decide**.
>
> **Continuous challenge** must be externalized. Self-challenge has a ceiling because blind spots are structural. Find the person who can say "you have an implicit assumption Y at point X," not the one who says "I don't think this works."
>
> **Pain is a signal.** When it appears, don't reach for a technical fix first — classify it: boundary of competence / cognitive blind spot / false assumption / uncontrollable. Misclassify and everything downstream is wrong. The only thing pain can be exchanged for is a principle you can use next time.
>
> Models are the variable, the harness is the constant, principles are the foundation. When you've generated more than you can be responsible for, you're no longer reviewing — you're betting it's fine.
