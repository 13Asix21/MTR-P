---
type: note
tags:
  - AI-Agent
  - agent-management
  - Claude-Code
  - vibe-coding
created: 2026-11-11
source: https://x.com/addyosmani/status/2117512471930167377
related: "[[PRINCIPLES-en]]"
---

[中文版](reference-addy-osmani-zh.md) · [← Back to README](../README.en.md)

# Addy Osmani: Ten Rules for Managing Agents

> **Source**: Addy Osmani (Anthropic, Claude Code team), X long-form article *Your coding agents made you a manager*, 2026-11-08
> **Original**: [x.com/addyosmani](https://x.com/addyosmani/status/2117512471930167377)
> **Blog follow-up**: [addyosmani.com/blog/coding-agents-manager](https://addyosmani.com/blog/coding-agents-manager)
> **Core claim**: Engineers who spend all day directing coding agents are already managing. The people who get the most from agents aren't necessarily the best prompt writers — they're the ones who learned to manage.

---

## The ten rules (ordered by leverage)

### 1. Give the agent a runnable way to check its work

> The highest-leverage habit.

- Give the agent an explicit pass/fail signal: test suite, build exit code, linter, screenshot diff
- Require it to **show the verification result as evidence**, not just claim "done"
- Tooling: `/goal` lets a separate model check the objective each turn; a Stop hook blocks the turn from ending until typecheck passes; `/code-review` sends the diff to a sub-agent that never saw the conversation

### 2. State the context the agent cannot read from the repo

- It can read code, but not your intent, constraints, or business background
- Declare: who this is for, what problem it solves, what *not* to do, what "done" means
- Write a short requirements note before prompting — avoid **Intent Debt**: unclear goals and trade-offs get decided for you

### 3. Turn repeated corrections into CLAUDE.md entries

- Corrected the same thing twice? It belongs in `CLAUDE.md` / `AGENTS.md`
- Systematic accumulation: patterns, gotchas, style preferences — read every session, updated every session. Knowledge compounds
- Repeated workflows should become **skills**

### 4. Enforce only hard rules with hooks

**Two-tier rule system** (the critical distinction):

| Tier | Nature | Examples |
|---|---|---|
| **Request layer** (agent may weigh) | Soft constraint | prompts, CLAUDE.md, skills |
| **Enforcement layer** (always applied) | Hard constraint | hooks, deny rules, managed settings |

- Drop the old habits — ALL CAPS or "think harder" in a prompt is not enforcement
- Rules that truly must not break go in hooks (e.g. block turn completion until tests pass)

### 5. Agree on a plan before coding

- Align in plan mode first, then write
- A plan should contain: expected outcome, constraints, acceptance criteria
- Strong engineers get **more** leverage from agents, not less — spec quality determines output quality
- A vague spec multiplies errors across dozens of parallel runs; a precise spec multiplies into precise implementations

### 6. Delegate the task, never the judgment

> "Delegate tasks, not judgment. Judgment and taste are the final step you don't outsource."

- Safe to delegate: docs, test coverage checks, low-risk mechanical refactors
- Must stay with you: complex problems; anything touching auth, security, finance, or system permissions
- **Cognitive Surrender, three stages**:
  1. Offloading — work handed over, but you still understand and can judge the result
  2. Judgment transfer — AI answers become the default basis for the next step
  3. Surrender — even the judgment you should make gets handed over
- Long-term consequence: **Comprehension Debt** — code grows, your understanding doesn't

### 7. Reset the session after two failed corrections

- Two failed corrections on the same issue → clear the session, rewrite the prompt
- Don't pile more prompts onto a polluted context — it only pushes the agent further in the wrong direction
- If two rounds of prompt adjustment don't produce the result, revisit the requirements instead

### 8. Decompose: sub-agents / agent teams / worktree isolation

- **Sub-agents** for decomposition: spawn focused children with explicit briefs and file ownership
- **Agent teams** for parallelism: lead + teammates, coordinated via a shared task list
- **Git worktrees** for isolation: one worktree per agent, zero merge conflicts
- Task sizing: too small and coordination dominates; too large and nobody reports in time. Sweet spot = self-contained unit with a clear deliverable. 5–6 tasks per teammate
- **One file, one owner** — two agents editing the same file is an overwrite waiting to happen

### 9. Set WIP limits and define kill criteria

- **WIP limits**: cap concurrent agents. 3–5 is the sweet spot; beyond that you drown in reviews
- **Kill criteria**: agent stuck on the same error for 3+ iterations → stop and reassign
- Osmani's own setup: 4–5 background agents plus a few local sessions — and some days that's already too many
- Attention cost past the first few agents is the **Orchestration Tax**
- When you can't review it all: add automated checks or cut parallelism. **"When you've generated more than you can be responsible for, you're no longer reviewing — you're betting it's fine."**

### 10. Retrospect like a manager

Six-step production line:

```
Plan → Spawn → Monitor → Verify → Integrate → Retro
```

| Step | Focus |
|---|---|
| **Plan** | Spec + acceptance criteria. The spec is the leverage |
| **Spawn** | Build the team, assign agents, define boundaries |
| **Monitor** | Check every 5–10 minutes, clear blockers. Don't hover |
| **Verify** | Run tests, review code. **Verification is the bottleneck, not generation** |
| **Integrate** | Merge branches, resolve conflicts. Watch for boundary violations |
| **Retro** | Update AGENTS.md / CLAUDE.md. Compound learning |

- Measure PR cycle time, rework, reverts — **not** "how much code AI wrote"
- **Have the agent quiz you**: after a large change, ask it to summarize what changed and why, then generate questions. Pass the quiz before merging
- **Once a week, write code without the agent** — calibrate your actual level

---

## Appendix: defense checklist against cognitive surrender

From Osmani's *Cognitive Surrender* post:

1. **Write your expectation before reading the output** — what do you think should happen? Even a bad guess works
2. **Hunt for the blind spot** — after the AI answers, name one thing it probably didn't check
3. **Interrogate the hidden decisions** — "What did you decide that I didn't authorize?" "Where did you rely on assumptions?" "What might this break?"
4. **Don't just look at the green check** — what did it test? What didn't it test? Look at what ran and what it returned before reading the summary
5. **Keep the raw material** — in multi-agent relay, one agent's summary becomes the next one's input; information thins out. Preserve source material and evidence
6. **If you wouldn't sign it, don't ship it** — set the bar and hold it

---

## Key takeaway

> Agent orchestration is a management skill in disguise: clarity, delegation, feedback loops, async communication. You're no longer just writing code — you're building the factory that builds your software. A factory needs quality control, process documentation, and precisely specified inputs. **The spec is no longer a prompt; the spec is product thinking made explicit.**
