---
type: design
status: draft
tags:
  - MTR-P
  - research-system
  - experiment-design
  - process-engineering
  - bayesian-optimization
created: 2026-11-11
related:
  - "[[PRINCIPLES-en]]"
---

[中文版](dual-track-research-system-zh.md) · [← Back to README](../README.en.md)

# Dual-Track R&D: An AI-Driven Dynamic Research System

> **Goal**: Build a reusable AI collaboration mechanism for two kinds of R&D — **engineering implementation** and **process development**. Turn parameter-tuning know-how currently scattered across people's heads and chat logs into something **searchable, reproducible, drift-aware, and cumulative**.
>
> **Core claim**: R&D is fundamentally about **finding a steady state in a high-dimensional parameter space, and holding it**. AI's value isn't deciding parameters for you — it's making *finding* faster, *holding* observable, and *the path taken* an asset.

---

## 0. Two fundamentally different tracks

Get this straight first, or you'll apply the wrong method — **engineering intuition applied to process work will hit a wall.**

| Dimension | Engineering | Process |
|---|---|---|
| **Parameter nature** | Discrete / structured: architecture choice, dependency version, concurrency, cache policy, timeouts | Continuous / physical: exposure time, gain, threshold, focus, pressure, temperature, speed |
| **Cost of trial** | Low. `git revert` in minutes | High. Materials, labor, possible scrap |
| **Parallelism** | High. Multiple git worktrees simultaneously | Low. Equipment is usually serial, one at a time |
| **Noise** | Low. Mostly deterministic | High. Environment, equipment drift, batch variation |
| **Feedback latency** | Seconds to minutes | Hours to days |
| **Parameter coupling** | Weak. Changing concurrency rarely changes log format | Strong. Changing exposure necessarily affects contrast, hence threshold behavior |
| **Experiment budget** | Tens to hundreds | Single digits to tens (scarce) |
| **Steady-state criteria** | Tests green + performance target + maintainability | CPK ≥ 1.33 + N consecutive good parts + within fluctuation band |
| **Main risk** | Over-engineering, tech debt, metric gaming | Overfitting to the current batch, production drift |
| **Where AI matters most** | Parallel exploration + automated verification | DOE design + surrogate models + drift monitoring |

> **In one line**: engineering is **cheap multi-point search**; process is **expensive few-point inference**. The former scales with compute; the latter scales with models that save experiments.

---

## 1. The shared six-ring loop

Both tracks share one loop; only the implementation of each ring differs.

```
   ┌──────────────────────────────────────────────────┐
   │                                                  │
   ▼                                                  │
① Variable space modeling                             │
   ↓                                                  │
② Design of experiments (DOE)                         │
   ↓                                                  │
③ Execution & capture (with full context)             │
   ↓                                                  │
④ Surrogate model / response surface                  │
   ↓                                                  │
⑤ Search decision (where's the next point?)           │
   ↓                                                  │
⑥ Steady-state check ──not met──→ back to ①/⑤         │
   │                                                  │
   └─met─→ steady-state monitoring ──drift──→ back to ①┘
```

**Every ring's output must be persisted**, or the loop breaks inside someone's memory.

| Ring | Artifact | MTR-P landing zone |
|---|---|---|
| ① Variable space | `params.yaml`: name/type/range/coupling/adjustability | P (config) |
| ② DOE | `doe-plan.csv`: points to run this batch | P (script output) |
| ③ Capture | Raw data + metadata (version/env/time/operator) | P (artifact, SHA-256) |
| ④ Surrogate | Model file + goodness of fit + applicability range | P (model) + T (method card) |
| ⑤ Search | Recommended next batch + rationale (acquisition value) | P (script output) |
| ⑥ Steady state | Criteria definition + monitoring records + drift alerts | M (criteria) + T (records) |

---

## 2. ① Variable space modeling

### 2.1 Standard structure for a parameter inventory

```yaml
# params.yaml — example; parameter names are placeholders
meta:
  project: <name>
  track: engineering | process        # decides the downstream workflow
  version: 0.1.0
  updated: 2026-11-11

params:
  - name: <param>
    type: continuous | discrete | categorical
    unit: <unit>                       # required for continuous
    range: [<low>, <high>]             # or values: [...] for discrete/categorical
    default: <value>
    adjustable: true | false           # false = locked for this study
    cost: low | medium | high          # cost of adjusting (critical for process)

coupling:                              # required for process track
  - from: <param A>
    to: <param B>
    relation: "A↑ causes B↓"           # natural language, cite the source
    verified: true | false

objective:
  primary: <metric>
  direction: maximize | minimize
  weight: 1.0

constraints:                           # hard constraints, never violated
  - expr: "<metric> <= <threshold>"
    reason: <why this cannot be violated>
```

**The process track must fill `coupling`.** Turning knobs without knowing the couplings is building on quicksand.

### 2.2 How to surface couplings

1. Run a **single-variable sweep** (OFAT — only for qualitative coupling discovery, never for optimization)
2. Watch which other metrics move when one parameter moves
3. For each suspected coupling, have an agent explain *why* it exists physically
4. **If you can't explain it but can demonstrate it, keep it with `verified: false`** — don't delete what you don't understand

> ⚠️ Writing "I guess" in `coupling` is the most dangerous thing you can do. Unverified couplings must be labeled, or downstream optimization builds on a false premise (see principle 4, intent debt).

### 2.3 Special handling for the engineering track

Many engineering "parameters" aren't numbers — they're **choices**:

| Type | Example | Handling |
|---|---|---|
| Discrete & enumerable | Database selection | Treat as categorical, one branch each |
| Continuous & tunable | Connection pool size | Continuous, but beware plateaus |
| Structural | Sync → async | **Not a parameter, an architecture decision.** Separate ADR |

**Don't mix structural changes into parameter search.** Evolution and exploration are different jobs; mixing them yields a pile of incoherent half-solutions.

---

## 3. ② DOE

### 3.1 Strategy per track

| | Engineering | Process |
|---|---|---|
| Initial | **Parallel full factorial** (cheap — run them all if you can) | **Fractional factorial / Plackett-Burman** (screen main effects) |
| Middle | Rank by metric, eliminate the weak | **Response surface (RSM) / Latin hypercube** |
| Late | Local fine-tuning | **Bayesian optimization** (update recommendation after each run) |
| Points per batch | 5–20 (run in parallel) | 3–8 (equipment cadence) |

### 3.2 Key rule: never change one factor at a time

**OFAT is the most common process-R&D mistake**: it cannot detect interaction effects, and experiment count is O(n) instead of O(log n).

> Example: raising exposure increases contrast (good), but raising threshold decreases contrast (bad). Each is monotonic in isolation, yet together they form an intermediate optimum band — **OFAT will never find that band**.

### 3.3 Working with AI

**AI generates the design; the human reviews cost:**

```markdown
# DOE agent brief template

Given params.yaml and this batch's budget (N experiments), output:
1. Recommended design (fractional factorial / RSM / LHS / BO) with rationale
2. The concrete point table (CSV)
3. Which metrics to capture at each point
4. What questions this batch can answer
5. What this batch **cannot** answer ← mandatory, prevents over-interpretation
```

Item 5 is non-negotiable. **Every batch must state what it cannot prove**, or the surrogate model gets extrapolated beyond its valid region.

---

## 4. ③ Execution & capture: context matters more than data

### 4.1 Minimum record per run

```json
{
  "run_id": "<unique>",
  "timestamp": "<ISO8601>",
  "track": "engineering | process",
  "params": { "<param>": "<value>" },
  "env": {
    "code_commit": "<git sha>",
    "config_hash": "<sha256>",
    "machine": "<equipment/machine id>",
    "ambient": { "<temp/humidity/etc>": "<value>" }
  },
  "raw_data_ref": "artifact://<path>",
  "raw_data_sha256": "sha256:<hash>",
  "metrics": { "<metric>": "<value>" },
  "notes": "<anomalies/observations>",
  "operator": "<human or agent id>"
}
```

**A run record without `code_commit` + `config_hash` is worthless data** — in three months you can't reproduce it, or tell whether a difference came from parameters or from a version change.

### 4.2 Engineering: let CI do it

Hook it into CI; don't ask humans to fill forms:

```yaml
- run: <benchmark/test>
  on_success: emit_run_record()
```

**Hand-filled tables are guaranteed to be incomplete in three months.**

### 4.3 Process: human-machine division

| Step | Who | Why |
|---|---|---|
| Parameter setting | Human | Equipment SOP, safety |
| Data capture | Script / device interface | Avoid transcription errors |
| Ambient logging | Automatic | Humans won't remember |
| Anomaly judgment | Human | On-site perception is irreplaceable |
| Result entry | Script reads device export | Fewer handoffs |

> **The data most often lost in process work is "environment" and "anomalies."** A 2°C difference, equipment just serviced, new material batch — omit these and the fitted model is mostly noise.

---

## 5. ④ Surrogate model (core for process, optional for engineering)

### 5.1 Why

Process experiments are scarce (maybe 20 total). You can't search a high-dimensional space directly. A surrogate fits a response surface from few points so that search happens **on the model**, not on the equipment.

### 5.2 Model selection

| Data points | Recommended | Why |
|---|---|---|
| < 15 | Gaussian process (GP) | Built-in uncertainty, ideal for Bayesian optimization |
| 15–50 | GP or random forest | RF is more stable but has no uncertainty |
| > 50 | Gradient boosting / small MLP | Expressive enough; add separate uncertainty estimation |
| Known physics | **Hybrid** (physics + residual learning) | Best. Physical constraints prevent runaway extrapolation |

### 5.3 Three numbers you must report

1. **Goodness of fit** (R² / RMSE) — least important
2. **Leave-one-out CV error** — more important; reflects generalization
3. **Applicability range** (boundary of the sampling hull) — most important; prevents extrapolation

> **A surrogate is only trustworthy inside the region enclosed by sampled points.** Outside it, what you get is hallucination — wearing a confidence interval as a disguise.

### 5.4 Does engineering need one?

Usually not. Engineering experiments are cheap; just run them for real. **Exception**: when a single run is very slow (e.g. a 4-hour load test), it's worth it.

---

## 6. ⑤ Search decision: where's the next point?

### 6.1 Acquisition functions

Process uses Bayesian optimization; the core is balancing exploitation and exploration:

| Strategy | Intuition | When |
|---|---|---|
| **EI** (expected improvement) | Expected gain over current best | Default first choice |
| **UCB** (upper confidence bound) | Mean + κ × std | Raise κ for more aggressive exploration |
| **PI** (probability of improvement) | Probability of beating the best | Near convergence |

**Exploration parameters like κ or ξ need an explicit rationale** — not a default.

### 6.2 Engineering search: no Bayes needed

Parallel runs plus ranking and elimination are more efficient:

```
Batch 1: 8 candidate approaches in parallel (separate worktrees)
  ↓ run each validation
Batch 2: keep top 3, each with 2 variants
  ↓
Batch 3: 1 winner, boundary testing
```

A simplified genetic algorithm. **Don't reach for Bayesian optimization because it sounds advanced — the engineering bottleneck was never sampling efficiency, it's verification bandwidth** (see principle 8, WIP limits).

### 6.3 Recommendations must be challengeable

Every recommendation must include:

- The point's coordinates + acquisition value
- **Why this point** (exploiting current best? exploring uncertain region?)
- **If this point fails, what does that tell us?** (falsifiability)

See principle 22 (continuous challenge) — **if the rationale can't be written, don't act on the recommendation**.

---

## 7. ⑥ Steady-state judgment and holding (the easiest ring to fudge)

### 7.1 Criteria must be hard-coded

**"It feels about right" is not a steady state.** Four conditions must all hold:

```yaml
steady_state:
  metrics:                     # 1. Metrics met
    - name: <metric>
      target: <value>
      tolerance: <±tol>        # tolerance is mandatory; without it, it's not a criterion

  window:                      # 2. Observation window
    samples: 30                # consecutive parts/runs
    duration_min: 120          # or consecutive duration

  stability:                   # 3. Stability (not just a good mean)
    cpk_min: 1.33              # process
    std_max: <limit>           # engineering: jitter
    trend_test: "no_drift"     # slope not significant

  exit_criteria:               # 4. Exit conditions (what counts as loss of control)
    - "<metric> out of spec 3 consecutive times"
    - "<metric> mean shift > 2σ"
    - "ambient outside <range>"
```

**Miss one and you effectively have no steady-state definition.**

### 7.2 Three-state machine with drift trigger

```
        ┌─────────────┐
        │ Exploration │  wide sampling, find the feasible region
        └──────┬──────┘
               │ feasible region found
               ▼
        ┌─────────────┐
        │ Convergence │  local refinement toward optimum
        └──────┬──────┘
               │ steady-state criteria met
               ▼
        ┌─────────────┐
        │  Steady     │  parameters locked, continuous monitoring
        └──────┬──────┘
               │ drift detected
               ▼
        back to Exploration (with historical prior — not from zero)
```

**Key design: after drift, return to exploration carrying historical data as a prior.** Not a restart — "I roughly know where it is, but need to reconfirm."

### 7.3 Drift detection methods

| Method | Signal | Best for |
|---|---|---|
| Control chart (X-bar / EWMA) | Slow mean drift | Process, first choice |
| Residual monitoring | Actual deviating from surrogate prediction | When a surrogate exists |
| Distribution test (KS / PSI) | Data distribution shifted | New batch / new material |
| Ambient correlation | Metric anomaly coincides with ambient anomaly | Root-cause investigation |

**Alerts must be graded**:
- 🟡 Minor offset → log, no action
- 🟠 Trend shift → trigger fine-tuning recommendation
- 🔴 Out of spec → stop the line, human intervention

See principle 12 (detect & observe) — **a change isn't done until the system can observe its effect**.

---

## 8. Interface with MTR-P

This research system isn't standalone; it should grow on MTR-P:

| Research object | Destination | Why |
|---|---|---|
| Variable inventory, steady-state criteria, valid parameter ranges | **M store** | Verified, reusable, cross-project searchable |
| Scripts, configs, raw data, models | **P store** (Git + Artifact) | Version control and reproducibility |
| Method cards (how you found it, what you tripped on) | **T store** | Draft methodology for human curation |
| Failed parameter combinations, dead ends | **R store** | The **negative-sample history** — precious |
| Cross-store retrieval | **Knowledge router** (principle 19) | New intent: `experiment_design` |

### 8.1 New router intent

```yaml
read:
  intents:
    experiment_design:
      plan: [P, M, T]          # past experiments first, then verified methods
      weights: { P: 1.0, M: 0.8, T: 0.6 }
      fallback: [R]            # also consult the negative samples
```

**Falling back to R is the critical design** — before proposing something new, check "what we tried before that didn't work," to avoid repeating dead ends (matching principle 21's data sourcing).

---

## 9. Roadmap (four stages — don't do it all at once)

### P0: Manual loop (1–2 weeks)

Goal: **capture the data completely first**. No automation yet.

- [ ] Define `params.yaml` (even if only 5 parameters)
- [ ] Define steady-state criteria (all four conditions)
- [ ] Hand-fill a run record per experiment (JSON template)
- [ ] Simplest possible visualization

**Acceptance**: you can answer "what were the parameters, environment, and results of the last run?"

### P1: Scripted capture (2–3 weeks)

- [ ] Experiment scripts emit run records automatically
- [ ] Raw data into artifacts with SHA-256
- [ ] Auto-generated DOE point table (human-reviewed)
- [ ] Basic dashboard

**Acceptance**: no manual transcription; records exist as soon as a run finishes.

### P2: Surrogate + recommendation (3–4 weeks)

- [ ] Fit a surrogate (start with GP)
- [ ] Report LOO error and applicability range
- [ ] Bayesian optimization recommends the next point (executed after human review)
- [ ] Recommendation includes falsifiable rationale

**Acceptance**: recommendations outperform random selection significantly (validated by backtesting on historical data).

### P3: Automated loop (cautious; human gate required)

- [ ] Automatic parameter dispatch (only within safe ranges)
- [ ] Graded automatic drift alerts
- [ ] Automatic return to exploration (with prior)
- [ ] **Human gate**: any parameter involving material cost or safety hazard must be human-confirmed

**Acceptance**: one week of unattended operation with stable metrics, and alert precision > 90%.

> **P3 is optional.** P0 + P1 already capture 80% of the value — once the data is complete, every downstream analysis becomes possible.

---

## 10. Risks and boundaries (what this cannot do)

| Risk | Description | Mitigation |
|---|---|---|
| **Runaway extrapolation** | Outside the sampling region it's hallucination | Mark applicability range; forbid recommendations outside it |
| **Overfitting the current batch** | Fails when material or environment changes | Require cross-batch validation; single-batch results don't count |
| **Steady-state misjudgment** | Window too short, happened to catch a good stretch | Don't relax window length or CPK threshold to "pass faster" |
| **Safety risk of full process automation** | A parameter combination may damage equipment or injure people | Safety-critical parameters always go through a human gate |
| **AI fabricating physics** | Coupling explanations sound plausible but are wrong | Couplings must be empirically demonstrated; `verified: false` cannot anchor optimization |
| **Wasted experiments** | Poor DOE hits irrelevant dimensions | Every batch must state what it can and cannot prove |

**One bottom line**: **this system makes human judgment more accurate; it does not replace it.** Any decision involving cost, safety, or irreversibility gets a human veto (principle 13 least privilege + principle 3 delegate tasks, not judgment).

---

## 11. Mapping to the 23 principles

| Principle | How it shows up here |
|---|---|
| 4 Spec first | `params.yaml` + steady-state criteria = the research spec |
| 5 Trace the chain | Couplings must be traced to mechanism, not just observed |
| 8 Parallel thinking | Have a hypothesis before prompting (which parameter is the main driver) |
| 10 Deep retrospective | After each batch: what did we learn, what changes next |
| 12 Detect & observe | Drift monitoring + complete run metadata |
| 14 Reversibility | Locked parameters must be restorable in one step |
| 17 Human curation | Method cards written by humans; AI only drafts |
| 18 Evidence retention | Raw data + SHA-256, not just aggregates |
| 19 Knowledge routing | New `experiment_design` intent |
| 21 Credibility weighting | Fall back to R negative samples; AI recommendations weigh less than measurements |
| 22 Continuous challenge | Every recommendation needs a falsifiable rationale |

---

## Key takeaway

> **The engineering bottleneck is verification bandwidth; the process bottleneck is experiment count.** The former scales with parallelism and automation, the latter with surrogate models that save experiments. Get this backwards and you'll burn both money and time.
>
> **A run record without `code_commit` + `config_hash` is worthless data.** In three months you can't tell whether a difference came from parameters or a version change.
>
> **Steady-state criteria need all four: metric + tolerance + observation window + exit condition.** Miss one and you effectively have no definition — and "no definition" turns acceptance into an argument.
>
> **A surrogate is only trustworthy inside the sampling hull.** Outside, it's hallucination wearing a confidence interval.
>
> **After drift, return to exploration carrying the historical prior.** Not a restart — "I roughly know where it is, but need to reconfirm."
>
> Last line: this system makes human judgment more accurate; it does not replace it. Cost, safety, and irreversibility get a human veto.
