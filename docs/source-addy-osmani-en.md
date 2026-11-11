---
type: source-collection
tags:
  - AI-Agent
  - agent-management
  - Claude-Code
  - source-text
  - Addy-Osmani
created: 2026-11-11
source:
  - https://addyosmani.com/blog/coding-agents-manager
  - https://addyosmani.com/blog/cognitive-surrender
  - https://addyosmani.com/blog/code-agent-orchestra
related: "[[reference-addy-osmani-zh]]"
---

# Addy Osmani Agent 管理系列原文合集

> 本文件收录 Addy Osmani 关于 AI Agent 管理/编排的三篇核心博客原文。
> 发布时间跨度：2026-01 至 2026-11
> 作者身份：前 Google Chrome DevTools/Lighthouse 工程师，现 Anthropic Claude Code 团队

---

# 原文一：Your AI coding agents need a manager

> **来源**：https://addyosmani.com/blog/coding-agents-manager
> **发布**：2026-01-08
> **主题**：Agent 编排的管理学映射——从调试上下文到管理团队

The skills that make someone a strong tech lead or manager translate directly to AI coding. Once you're running multiple agents in parallel, you stop just debugging context and start managing a team. Clarity, delegation, verification loops, async communication. The shape of the work is changing as orchestration becomes more mainstream.

In the near future, I think the highest-leverage developers will look like async-first managers running a small fleet of parallel AI coding agents. Agents can now do meaningful work in the background, in isolated environments, and return with something reviewable - often a pull request. We of course require quality checks, but the bottleneck is no longer "can the agent write code?" It's "should we build this?" and "can I manage multiple agents doing so effectively?".

We're seeing the workflows that foreshadow this future. A recent thread from Boris Cherny, Claude Code's creator, went viral because it made the shift feel concrete: he runs five Claude Code sessions locally in terminal tabs, plus another five to ten in the browser, and even starts sessions from his phone to check on later. That's orchestration.

At the same time, thoughtful engineers have been documenting the parallel agent lifestyle from a more skeptical angle. Simon Willison's take is the one I keep coming back to: the natural bottleneck isn't generating code - it's reviewing it. He still found real value in firing off parallel tasks, so long as you're honest about your attention span and choose tasks that don't overload your brain. I've been building similar workflows and agree: the value is real, but only if you treat it like management, not magic.

This is the mindset shift: you're no longer pairing with a single agent. You're running a small team.

This is the reason the skills that make someone a strong tech lead, engineering manager, or engineering leader suddenly translate directly into "being good at AI coding." Because AI coding at scale stops being a prompting problem and becomes a management problem.

## A mental model for the new workflow

I think about two modes running side-by-side.

The first is **local, high-touch sessions where you stay human-in-the-loop**. These are for architecture decisions, tricky refactors, product nuance, ambiguous requirements, and anything where taste and judgment dominate. You're pairing with the agent, course-correcting in real time, and making calls that require context the agent doesn't have.

The second is **cloud or background sessions that run asynchronously**. These are for focused, bounded tasks: straightforward features, migrations with clear patterns, test generation, documentation updates, dependency bumps, small bugfixes, and targeted refactors. You fire them off, context-switch to something else, and come back to review the output.

This split maps cleanly onto how modern tools are evolving. Cloud agents are explicitly positioned as parallelizable, sandboxed tasks that can write code, run commands, and propose changes for review.

GitHub's Copilot coding agent frames itself similarly - an asynchronous background agent that opens a draft PR, works in the background, then requests review where you can comment and have it iterate. And platforms are moving toward "mission control" dashboards for multiple agents, not just one. GitHub even previewed "Agent HQ" as a control plane for coordinating multiple third-party coding agents in one place, including running them in parallel on the same tasks to compare outputs.

So the question becomes less "which model writes the best code?" and more "can I run this like a high-performing team?"

That's where manager skills show up.

## Why manager skills make you a better AI coder

When you manage humans, you learn the hard way that output quality is shaped upstream. Ambiguity, fuzzy ownership, missing definitions of done, and lack of feedback loops create churn. The same is true - maybe even more true - when your "team" is a set of fast, non-human agents.

Anthropic's best-practices guide for Claude Code says this plainly: specificity up front materially improves success rate and reduces course correction. That's manager language, even if it's written for a tool.

Here are the four skills that transfer most directly.

### Clear task scoping: Write a brief, not a vibe

If ambiguity kills agent effectiveness, then your job is to turn idea-shaped thoughts into a brief that can survive contact with implementation. This is exactly what good managers do: convert intent into something executable.

A practical agent brief covers: the outcome (what should be true when we're done), the context (where in the codebase this lives and what patterns exist), the constraints (performance, security, API shape, dependency rules, style conventions), the non-goals (what we're explicitly not doing), acceptance criteria (concrete checks like tests passing or endpoints behaving a certain way), integration notes (which files are off-limits and where seams should be), and a verification plan (how we'll know it works).

This is the difference between getting a clean PR and getting a thousand-line mess you can't trust.

Two tactics make this dramatically easier. First, point agents at existing patterns - Anthropic's guide explicitly recommends mentioning the files you want the agent to follow or update, so it anchors on real conventions instead of inventing its own.

Second, put durable rules somewhere agents can always find them. Now standard, OpenAI's Codex docs describe using your `AGENTS.md` file to give consistent expectations about tests to run, lint rules, dependency policies, and documentation requirements. If you've ever onboarded someone new to a codebase, this will feel familiar: give them the map, the conventions, and the definition of done before they start writing.

### Delegation: Decide what to fully hand off vs. what needs your judgment

A surprising trap with agents is over-delegating the parts that are actually human work: product intent, API design tradeoffs, architectural boundaries, and long-term maintenance calls.

OpenAI's "AI-native engineering team" guide frames this as a three-part split across the software development lifecycle: delegate, review, own. Even in their optimistic view, engineers keep ownership of final decisions and sign-off, especially for ambiguous problems.

That maps cleanly onto orchestration. Some work you can fully delegate - mechanical implementation with a crisp spec, boilerplate refactors, test generation with strong review, docs updates, and low-risk maintenance chores. Other work you delegate but stay in the loop with checkpoints - anything touching shared interfaces, anything likely to create merge conflicts, anything with tricky product edge cases, anything involving data migrations. And some work you simply don't delegate, or delegate only to explore options - system architecture and new abstractions, cross-cutting refactors that require taste, product decisions and "should we build this?" calls, and security-critical or privacy-sensitive design decisions.

This is the same muscle as assigning work on a team: you're constantly deciding who owns what, how much autonomy is safe, and where you want a checkpoint before the work hardens.

### Verification loops help to catch issues early

When you manage people, you learn that the fastest way to lose time is reviewing low-quality work too late. You want early feedback loops and quality gates. Agents are the same - except they can generate low-quality work at high speed.

Verification loops are the unlock, and the best toolmakers are baking them into their guidance. Anthropic's best practices explicitly suggest running multiple Claude instances where one writes code and another verifies via review and tests, treating it like a two-person workflow with separation of concerns. OpenAI's Codex positioning is also explicit about tool use: run commands, run tests, iterate to passing states, then propose a PR.

What verification loops look like in practice: require the agent to run the test suite (or a scoped subset) and include the output in its final message. Require lint and typecheck to pass for touched areas. Require the agent to add or modify tests for behavior changes. Require a structured "PR packet" at the end - a summary of changes, why this approach, files touched, test plan plus results, and risks or follow-ups.

If you want to go one step further, use the two-agent pattern: Agent A implements, Agent B reviews for correctness, style, edge cases, and missed tests, then Agent A (or a fresh Agent C) applies the review feedback and re-runs verification. That's not theoretical - Anthropic literally recommends it as a workflow upgrade.

This is also where manager instincts matter: you're not just checking that code compiles. You're checking whether it aligns with conventions, whether it's maintainable, and whether it matches intent.

### Async check-ins: Treat agents like reports

If you're running ten parallel sessions, you cannot afford to scroll through each one trying to understand what happened. You need lightweight, repeatable check-ins. This is the async-management playbook applied to agents.

Set a check-in cadence: "If you haven't made significant progress in 15 minutes, stop and report blockers." Ask for status in a predictable format: What changed? What's next? What are the risks or blockers? What do you need from me?

This sounds small, but it's how you keep parallel work from going off the rails without constantly babysitting. If you want a concrete analogy, managing agents looks a lot like managing a distributed team across time zones. You front-load clarity, you rely on written updates, and you reserve real-time attention for decisions and unblockers.

## The hard parts are real (and they're management problems)

### Merge conflicts multiply fast

Parallel agents touching adjacent code is like putting five engineers in the same file without coordination. It's not a tooling failure; it's a boundary failure. The fix is exactly what you'd do on a human team: create intentional task boundaries, isolate work, and define interfaces.

Practically, that often means running agents in isolated working directories or branches. Git worktrees are a clean pattern here - they let you check out multiple branches at once from the same repo, each in its own directory. Claude Code's docs explicitly recommend using git worktrees for parallel sessions so instances don't interfere with each other, and Anthropic's best practices give a step-by-step for using worktrees plus "one terminal tab per worktree" as an operating pattern. Even if you're using cloud agents, you want the same principle: separate branches, small PRs, and clear ownership of modules.

My favorite boundary rules, borrowed from team coordination: one agent owns one PR, no mega-PRs from multiple agents. If two agents might touch the same files, redesign the task split. Shared interfaces get handled in a first PR (human-led), then agents build on top of that seam.

### Taste and "should we build this?" becomes the real bottleneck

When building gets cheap, you start building everything. That feels productive right up until your product becomes a junk drawer of features.

This is the part of the shift that doesn't get enough attention: AI doesn't remove the need for judgment; it raises the value of judgment. "Should we?" starts to matter more than "can we?"

That is a manager skill in disguise: prioritization, saying no, defining success, running small experiments, and killing bad ideas quickly.

If you want to operationalize this, borrow two concepts from management. First, WIP limits: cap how many active agent streams you allow at once, so you don't drown in reviews (the same bottleneck Willison called out). Second, kill criteria: define what would cause you to stop pursuing a feature before you start building it.

## What I'm enjoying (and why this is worth the effort)

The "on the go" delegation effect is real. When background agents can work against a repo and come back with reviewable changes, you move from "file an issue for later" to "start building the idea now."

My current setup runs four to five background agents handling low-to-medium complexity work while I stay human-in-the-loop locally for architecture and nuanced product work across another three to five sessions. The ability to delegate work from mobile has shifted my creative loop: idea to implementation draft to review to iterate.

That is incredibly gratifying when you have a quality bar and a process, because your creative loop tightens. But it only stays gratifying if you treat orchestration like management, not like magic. You have to be realistic about your bandwidth to multi-task and context switch. Not everyone needs ten to fifteen agents running at once. Boris's workflow is useful as an extreme that shows what's possible, not as a requirement.

## A simple operating system for orchestration

If you want one repeatable loop to practice:

Plan like a manager - write the brief with outcome, constraints, and acceptance criteria. Spawn like an orchestrator - assign tasks to parallel agents with explicit boundaries. Monitor async - lightweight check-ins, unblock quickly, avoid mid-flight thrash. Verify aggressively - tests, lint, structured PR packets, second-agent review where useful. Integrate carefully - merge in a deliberate order, watch for boundary violations. And retro - update your AGENTS.md and checklists so the next run starts smarter.

For most of us, the sweet spot looks closer to a handful of background agents doing low-to-medium complexity work while you stay human-in-the-loop for architecture and product nuance, realistic about how much context switching you can tolerate.

That's orchestration. And the fastest way to get good at it is to learn the same skills that make someone effective at leading engineers: clarity, delegation, feedback loops, and async communication.

---

# 原文二：Cognitive Surrender

> **来源**：https://addyosmani.com/blog/cognitive-surrender
> **发布**：2026-11-04
> **主题**：认知投降——AI 输出悄然成为你的输出，而你觉得没什么需要检查的了

*Cognitive offloading is delegating to the AI and still owning the answer. Cognitive surrender is when the AI's output quietly becomes your output and there is nothing you feel is left to check. For software engineers the line between the two moves under your feet most days, and most of us are crossing it without noticing.*

There's a term I heard yesterday that I wanted to discuss: **cognitive surrender**. It comes from a recent paper out of the Wharton School at UPenn - Steven Shaw and Gideon Nave's *"Thinking - Fast, Slow, and Artificial: How AI is Reshaping Human Reasoning and the Rise of Cognitive Surrender."* The phrase has older theological roots, but the AI framing is new and it lands hard for anyone shipping code with an agent at their elbow.

Their distinction is the part worth memorising:

- **Cognitive offloading** is the calculator, the search engine, the GPS. You hand off the *how* and keep the *what*. You still judge whether the result is sensible, and you intervene when it isn't.
- **Cognitive surrender** is what happens when you stop constructing the answer at all. The AI's output becomes your output. There's nothing to override, because you never formed an independent view to compare it against.

Across three experiments and 1,372 participants, Shaw and Nave found that simply having an AI available was enough for people to surrender. On trials where the AI was wrong, **73% of the time participants accepted the wrong answer.** Worse: their *confidence* went up when AI was available, even though half the answers were deliberately incorrect. They were borrowing the model's confidence (which is always quite high) and treating it as their own.

That borrowed-confidence effect is where this stops being a general cognition story and starts being a software engineering one.

## Where surrender shows up in our work

Most of us don't surrender on the easy stuff. We notice when an agent invents an API or fabricates an import. The surrender happens further down the stack, in the moments where the cost of forming an independent view feels disproportionate to the task.

A few places I've watched it happen, mostly to me:

**Reading the diff.** The agent produces a 600-line PR. You scan it. The variable names are reasonable. The tests are green. You approve. Somewhere in the middle there's a subtle ordering change in a transaction boundary, or a default that flips for an edge case you didn't think to look at. You didn't review the code. You ratified it. The surrender was the absence of a decision.

**Debugging an error you don't fully understand.** The stack trace looks scary. You paste it into the agent. It returns a fix. The fix works. You move on. Two weeks later a related symptom resurfaces and you realise you never actually understood the original bug. You only removed its visible expression. The system's mental model in your head is now wrong in a place you can't even point to.

**Making a design call.** You're not sure whether to use a queue or a direct call between two services. You ask the agent. It picks one with a confident-sounding paragraph of justification. You go with it. You didn't reason about your throughput, your failure modes, your replay semantics. You took the model's framing of the problem and the model's answer in the same gesture.

**Learning something new.** This is the one the Anthropic skill-formation paper puts numbers on. Engineers who used AI to *generate* code while learning a new library scored 17% lower on a follow-up comprehension quiz than the control group. Engineers who used AI for *conceptual inquiry* (asking questions, exploring tradeoffs) held their ground. Same tool. The posture changed the outcome.

The thread running through all of these is the same: the model offered a complete answer, and we accepted it instead of constructing a parallel view of our own. Sometimes that's correct. Sometimes that's surrender. The two feel identical from the inside.

## The connection to comprehension debt

I've written before about comprehension debt - the growing gap between how much code exists in your system and how much of it any human genuinely understands. Cognitive surrender is the *mechanism* by which comprehension debt accumulates.

Each act of surrender is a tiny loan. The codebase grows by another patch you don't fully understand. The architecture absorbs another decision you didn't make. The test suite gains a test you didn't think to specify. None of these feel like a problem on the day they happen. They compound.

MIT's *Your Brain on ChatGPT* paper showed the same pattern at the neural level: writers leaning on AI exhibited measurably reduced neural connectivity, weaker memory of what they'd just produced, and difficulty reconstructing their own reasoning. The authors called it *cognitive debt*, borrowed from technical debt - short-term gain, compounding long-term cost.

Put the two frames together. **Cognitive surrender is how you take on cognitive debt. Comprehension debt is the bill, denominated in lost mental model. The interest gets paid the next time something goes wrong and nobody on the team can reconstruct the system from first principles.**

The AI doesn't create the debt. The posture you bring to it does. The same model that hollows out one engineer's mental model can sharpen another's, depending on whether they're using it to think or instead of thinking.

## Why software engineers are unusually exposed

A few features of our work make us more vulnerable than the average knowledge worker.

**Surface signals look correct by default.** Generated code compiles. It passes the linter. It runs. It looks like the rest of the file. Most domains don't have such a strong "looks plausible" filter for the AI's output. Ours does, and it's the wrong filter. *Surface correctness is not systemic correctness*, and the gap between them is exactly where surrender hides.

**Throughput is the visible metric.** PRs merged, features shipped, tickets closed. None of these distinguish between "I built this and understand it" and "the agent built this and I approved it." The org rewards both equivalently in the short run. Surrender is invisible to the dashboard.

**Confidence transfers cleanly.** Models speak in declaratives. Code reviews tend to read declaratives as authority. When the agent writes "we use a debounce of 300ms here to avoid jank," it sounds like institutional knowledge, even if the model invented the number on the spot. You inherit its certainty without inheriting its (nonexistent) reasoning.

**The work composes.** Each surrender enables the next one. Once you've accepted a chunk you don't fully understand, the next change to that chunk is almost guaranteed to be another act of surrender, because forming an independent view now requires reconstructing the part you skipped. Surrender is path-dependent.

I'm not arguing against AI coding tools. The *posture* matters more than the tool, and we don't have many of the habits we need yet.

## The calibration question

Shaw himself is careful not to be alarmist about this. His framing is the one I'd repeat back to anyone using these tools seriously:

> Cognitive surrender is not the same as saying AI is bad or that using AI is irrational; in many settings, AI can improve judgment. The key issue is calibration: knowing when AI is helping you think and when it is quietly doing the thinking for you.

The question to keep asking yourself: *am I forming an independent view of this answer, or just adopting the agent's view wholesale?* Those are different psychological acts that look identical from the outside.

A few heuristics I've started using to keep myself on the offloading side of the line:

**Construct an expectation before reading the output.** Before I run the agent on a non-trivial task I write down (even just in my head) what I think the answer should look like. Three lines or fifty. A queue or a direct call. Whether the bug is in this module or that one. When the agent's answer matches my expectation, I'm calibrated. When it doesn't, I have a real choice to make: am I wrong, or is it? That choice is the thing surrender skips.

**Read the diff like the AI didn't write it.** Pretend a junior engineer on your team submitted the PR. Would you merge it on the strength of "the tests pass"? You wouldn't. The same standard should apply when the author is a model. The job hasn't changed; the author has. *"Seems right"* is still not a review.

**Ask the model to argue against itself.** Most models will produce a confident answer and then, when prompted, produce an equally confident counter-argument. That second pass is cheap and it breaks the borrowed-confidence effect. If you can't reason about which of the two answers is right, you've found a place where you were about to surrender.

**Notice when you're tired.** Surrender is a fatigue phenomenon. The first PR of the day gets a real review; the fifth one gets a glance. Senior engineers I trust have all converged on some version of "stop letting the agent generate when I'm too tired to evaluate." That self-knowledge is part of the job now.

**Watch where the confidence is coming from.** If you find yourself defending a design choice in a meeting and you can't actually reconstruct *why* it was made, only that the agent suggested it and it seemed reasonable, you've inherited the model's confidence without any of the reasoning underneath it. That's a surrender artifact. Go back to the code and rebuild the *why* before the conversation continues.

## Engineering moves that resist surrender

The personal heuristics matter, but there's a structural version of this too. Most of what I've been writing about over the past few months is about building scaffolding that makes surrender harder.

A short list of moves that hold up:

**Verification as a hard exit criterion.** Every agent-completed task should terminate in concrete evidence: a test that runs, a screenshot, a log, a runtime trace, a reviewer signoff. *"It looks done"* is the surrender-friendly exit. *"Here is the evidence it works"* is the surrender-resistant one. Bake the evidence requirement into the workflow and you remove the easiest path to surrender.

**Anti-rationalization tables.** The most distinctive design choice in Agent Skills is to pair each common excuse for skipping a workflow step with a written rebuttal. That doubles as a surrender-resistance mechanism. *"This task is too simple to need a spec."* → *"Acceptance criteria still apply."* You're pre-writing the rebuttal to the rationalization the model (or your tired Friday-afternoon self) hasn't yet produced. Models are exceptional at generating plausible reasons to skip the rigorous step. Anti-rationalization tables refuse to argue with them on the day.

**Smaller scope, smaller PRs.** Surrender scales with size. A 50-line change you can actually read; a 600-line change you cannot. Google's ~100-line PR norm exists for human reasons but it works against AI surrender for the same reasons. **The unit of review is the unit of comprehension. Make the unit small enough to actually comprehend.**

**Conceptual inquiry over generation, when learning.** This is the skill-formation paper's finding restated as a habit. When you're new to a library or a system, ask the agent to *explain* before you ask it to *generate*. The same tool, used to interrogate rather than to produce, builds rather than erodes your mental model. The data is unambiguous on this and the cost of switching modes is trivial.

**Friction by design.** The arXiv "Cognitive Agency Surrender" paper proposes *Scaffolded Cognitive Friction*: deliberately introducing moments of resistance to interrupt heuristic acceptance. In engineering terms: a required design doc before generation, a confirmation step before merge, a checklist before deploy. Friction has a bad reputation in productivity discourse. It's also exactly what stands between offloading and surrender.

**Solo time at the keyboard.** Write some code without the agent, every week. Not as a moral exercise; as a calibration exercise. The day you can't comfortably build something simple without AI assistance is the day the offloading became surrender and you didn't notice.

## Mutual amplification, not delegation

The frame I want to leave on isn't the bleak one. Andy Clark, quoted in *Time* on this research, draws the right distinction: there's a difference between *delegating to* an AI system and *cooperating with* it. Delegation produces surrender. Cooperation produces what he calls **mutual amplification**: a loop where your prompts sharpen the model's output, which sharpens your next prompts, which sharpens your model of the problem.

You can feel the difference. With mutual amplification you catch yourself learning the domain *through* the conversation, not in spite of it. You end the session with a sharper mental model than you started with, not a fuzzier one. You can still build the thing yourself; you've just chosen a faster path. The agent is the second engineer in the room, not the only one.

The surrender posture is the inverse. The agent ends with a sharper model of the problem than you do. You can't reconstruct the design. You can't debug the code without the agent's help. You've outsourced the part of the work that was supposed to be making *you* better.

Both postures use the same tools. Both produce code that ships. From the outside, on a single sprint, they look identical. The difference shows up six months in, when something breaks and one of the two engineers can fix it from first principles and the other can't.

## What I most want this post to do

Not to scare anyone off these tools. I use them every day. I've shipped more in the last twelve months than in any previous twelve, and I think the people sitting this out are making a much bigger mistake than the people leaning in.

But the posture matters, and we don't talk about it nearly enough. The conversation is mostly about what models can do. It needs to be at least as much about what we're doing with them, and whether the answer is *thinking with* or *not thinking at all*.

Cognitive offloading is a superpower. Cognitive surrender is the failure mode of using it without noticing the line between them. **The job, increasingly, is to stay calibrated about which side of that line you're standing on at any given moment.**

If your code is shipping and your understanding of the system is shrinking, you're paying with cognitive debt. If your code is shipping and your understanding of the system is *growing*, you're doing the actual job, just faster than before.

The tools are the same in both cases. The posture is what differs. That's the part still entirely yours.

---

# 原文三：The Code Agent Orchestra

> **来源**：https://addyosmani.com/blog/code-agent-orchestra
> **发布**：2026-03（O'Reilly AI CodeCon 演讲文稿）
> **主题**：多 Agent 编排模式——从 subagent 到 agent team 到规模化编排

I spoke about orchestrating coding agents at O'Reilly AI CodeCon today. The talk covered the landscape of patterns for coordinating AI coding agents in real-world software workflows - from spawning your first subagent to running parallel agent teams with quality gates that keep everything trustworthy. This is a write-up of the material I presented for anyone looking to put these patterns into practice.

## The moment we're in

**You used to pair with one AI. Now you manage an agent team.**

Six months ago, most developers worked with a single AI assistant in a tight synchronous loop. You typed a prompt, waited, reviewed the output, gave feedback, repeated. Your ceiling was whatever fit in that single context window. The conversation thread was your workspace.

That model has been replaced. Today, the most productive developers are coordinating multiple agents running asynchronously - each with its own context window, its own file scope, its own area of responsibility - while the developer orchestrates from above. The codebase becomes your canvas, not a conversation thread. This is the shift from being a conductor (one musician, real-time guidance) to being an orchestrator (an entire ensemble, asynchronous coordination).

## The 8 levels of AI-assisted coding

Most developers are stuck at Level 3-4. The orchestration tier starts at Level 6, and it requires a fundamentally different set of skills than what got you to Level 5.

## The shift: conductor to orchestrator

The core mental model shift is the difference between pair programming and managing a team. In the conductor model, you guide a single AI agent in real time. It's synchronous, sequential, and your context window is a hard ceiling.

In the orchestrator model, you're coordinating an entire ensemble. Multiple agents, each with their own context window, working asynchronously. You plan the work, assign it, and check in periodically.

## The single-agent ceiling

Every developer eventually hits three walls with a single agent: context overload, no specialization, and no coordination. Subagents solve the first two. Agent Teams solve all three.

**Context overload.** One agent can only hold so much information. Large codebases overwhelm a single context window.

**No specialization.** One agent doing everything - data layer, API, UI, tests - is a jack of all trades and master of none.

**No coordination.** Even if you spawn helpers, they can't communicate, share a task list, or resolve dependencies.

## Why multi-agent?

Four compounding reasons to go multi-agent:

- **Parallelism (3x throughput)** - Three agents building frontend, backend, and tests simultaneously.
- **Specialization (focused context)** - Each agent only sees the files it owns.
- **Isolation (safe execution)** - Git worktrees give each agent its own working directory.
- **Compound learning** - An AGENTS.md file accumulates patterns and gotchas across sessions.

## Pattern 1: Subagents - focused delegation

Subagents are the simplest multi-agent pattern and the one you should try first.

Subagents use the Task tool to spawn specialized child agents from a parent orchestrator. The parent decomposes a task into pieces, spawns subagents for each piece, and manages the dependency graph manually.

Example: You give Claude Code a prompt: "Build a bookmarks manager called Link Shelf using Express and SQLite." The parent orchestrator decomposes this into three subagent briefs:

1. **Data Layer subagent** - builds `db.js` with schema and CRUD operations, writes `DATA.md` report when done
2. **Business Logic subagent** - builds `validation.js` with input rules, writes `LOGIC.md` report when done
3. **API Routes subagent** - reads both `DATA.md` and `LOGIC.md`, builds `server.js` with Express routes

The first two subagents are independent - they run in parallel. The third depends on both, so it waits for their report files before starting.

**What subagents solve:** context isolation per agent, specialization, parallel execution for independent tasks.

**What is still missing:** the parent must manually manage the dependency graph. There's no peer messaging between agents. There's no shared task list.

### Pro-tip: Hierarchical subagents - teams of teams

Don't stop at one level of delegation. Spawn feature leads that spawn their own specialists. This gives you 3x deeper decomposition without exploding anyone's context window.

Instead of your orchestrator spawning six subagents - which fragments its context - spawn two feature leads. Each feature lead then spawns its own two or three specialists. This mimics how real engineering organizations work.

## Pattern 2: Agent Teams - true parallel execution

Agent Teams add the coordination primitives that subagents lack: a shared task list with dependency tracking, peer-to-peer messaging between teammates, and file locking to prevent conflicts.

Enable it with:

```
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

The architecture has three layers:

1. **Team Lead** at the top - decomposes work, creates the task list, synthesizes results
2. **Shared Task List** in the middle - tasks with statuses (pending, in_progress, completed, blocked), dependency tracking, and file locking
3. **Teammates** at the bottom - each an independent Claude Code instance with its own context window, running in tmux split panes

Teammates self-claim tasks from the shared list. They message each other directly - peer-to-peer, not through the lead. When a teammate finishes and marks a task complete, any blocked tasks that depended on it automatically unblock.

### Agent Teams: key takeaways

- **True parallelism with coordination** - shared task list with dependency tracking ensures work happens in the right order.
- **Peer messaging prevents bottlenecks** - teammates communicate directly.
- **Plan approval for risky tasks** - require teammates to write a plan before implementing.
- **Right-size your team** - 3-5 teammates is the sweet spot. Token costs scale linearly with team size.

### Pro-tips for Agent Teams: reliability

**Loop Guardrails + Reflection Step.** Every teammate gets a hard `MAX_ITERATIONS=8`. Before each retry, force a reflection prompt:

> "What failed? What specific change would fix it? Am I repeating the same approach?"

**Dedicated @reviewer Teammate.** Spawn a permanent reviewer:

- **Model:** Claude Opus 4.6 (read-only)
- **Tools:** lint, test, security-scan only
- **Trigger:** auto on every TaskCompleted event
- **Ratio:** 1 reviewer per 3-4 builders

The lead only sees green-reviewed code.

## Pattern 3: Orchestration at scale

The 2026 tool landscape breaks into three tiers:

### Tier 1: In-process subagents and teams
Claude Code subagents and Agent Teams. Single terminal session, no extra tooling needed. Start here.

### Tier 2: Local orchestrators
Your machine spawns multiple agents in isolated worktrees. Best for 3-10 agents on known codebases. Tools include Conductor, Vibe Kanban, Gastown, Claude Squad, Cursor Background Agents.

### Tier 3: Cloud async agents
Assign a task, close your laptop, return to a pull request. Tools include Claude Code Web, GitHub Copilot Coding Agent, Jules by Google, Codex Web by OpenAI.

## Quality gates: trust but verify

Three quality gates make agent output trustworthy:

**Plan approval.** Require teammates to write a plan before they start coding. The lead reviews the approach and approves or rejects. It's far cheaper to fix a bad plan than to fix bad code.

**Hooks.** Automated checks on lifecycle events. A `TaskCompleted` hook runs lint and tests before marking a task as done. If the hook fails, the agent keeps working until it passes.

**AGENTS.md for compound learning.** This file captures discovered patterns, gotchas, and style preferences. Every agent reads it at the start of a session, and every session adds to it.

## The Ralph Loop and self-improving agents

Popularized by Geoffrey Huntley and Ryan Carson, the Ralph Loop is the pattern behind "shipping while you sleep."

The five-step cycle:

1. **Pick** - select the next task from `tasks.json`
2. **Implement** - make the change
3. **Validate** - run tests, types, lint
4. **Commit** - if checks pass, commit and update task status
5. **Reset** - clear the agent context and start fresh with the next task

The key insight is stateless-but-iterative. By resetting each iteration, the agent avoids accumulating confusion. Small bounded tasks produce cleaner code with fewer hallucinations.

Safeguards: feed errors back for auto-retry, but kill and reassign after 3+ stuck iterations. Always work on feature branches. Set hard limits on iterations, time, and tokens.

### Making agents smarter over time

**Self-Reflection with REFLECTION.md proposals.** After every task, force the agent to write a REFLECTION.md: what surprised me, one pattern to add to AGENTS.md, one prompt improvement.

**Token Budgeting and Kill Criteria.** Set hard per-agent budgets. At 85% of budget, auto-pause and notify the lead. If stuck 3+ iterations on the same error, kill and reassign.

## The discipline: what makes this work

The human bottleneck was a feature, not a bug. At human pace, errors compound slowly and pain forces early correction. With an army of agents, small mistakes compound at a rate that outruns your ability to catch them.

### Delegate the tasks, not the judgment

Agents excel at anything with a tight evaluation function. Keep for yourself: architecture and API design, deciding what NOT to build, reviewing agent output with full system context.

Build fewer features, but the right ones. The speed of code generation is a siren song. Slow down enough to maintain understanding.

### Your spec is the leverage

When you orchestrate fifty agents in parallel, vague thinking doesn't just slow you down - it multiplies. A vague spec multiplies errors across the entire fleet. A precise spec multiplies into precise implementations everywhere.

The spec isn't a prompt anymore. The spec is product thinking made explicit. This is why strong software engineers get more leverage from these tools than weak ones.

## The factory model

You're no longer just writing code. You're building the factory that builds your software.

The six-step production line:

1. **Plan** - Write specs with acceptance criteria. Your spec is the leverage.
2. **Spawn** - Create your team and assign agents.
3. **Monitor** - Watch progress and resolve blockers every 5-10 minutes. Don't hover.
4. **Verify** - Run tests, review code. Verification is the bottleneck, not generation.
5. **Integrate** - Merge branches, resolve conflicts.
6. **Retro** - Update AGENTS.md with new patterns. Compound learning.

Practical tips for running the factory:

- **Set WIP limits:** Don't run more agents than you can meaningfully review. 3-5 is the sweet spot.
- **Define kill criteria:** If an agent is stuck for 3+ iterations on the same error, stop and reassign.
- **Async check-ins:** Check progress every 5-10 minutes. Let agents work autonomously.
- **One file, one owner:** Never let two agents edit the same file. Conflicts kill velocity.

## 5 patterns to start today

1. **Subagents for decomposition.** Use the Task tool to spawn focused child agents with specific briefs and file ownership. Zero setup. Start here today.
2. **Agent Teams for parallelism.** Enable `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`. Create a lead + 3 teammates. Use the shared task list for coordination.
3. **Git worktrees for isolation.** Each agent gets its own worktree. No merge conflicts, clean integration.
4. **Quality gates for trust.** Require plan approval for risky changes. Add hooks that run tests on task completion. Never trust agent output without verification.
5. **AGENTS.md for compound learning.** Document patterns, gotchas, and style preferences. Every session reads it, every session updates it. Knowledge compounds.

Start with pattern one today. Graduate to Agent Teams next week. Layer on quality gates and compound learning from there.
