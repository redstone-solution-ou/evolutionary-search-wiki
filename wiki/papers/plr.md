# Prioritized Level Replay

> **Short name:** `plr` · **arXiv:** [2010.03934](https://arxiv.org/abs/2010.03934) · **PDF:** [local](../../papers/plr_jiang_2010.03934.pdf) · **Date:** 2020-10 · **Venue:** ICML 2021

**Authors:** Minqi Jiang, Edward Grefenstette, Tim Rocktäschel (Facebook AI Research / University College London)

## Abstract
Prioritized Level Replay is an alternative approach to unsupervised
environment design that drops the learned adversary entirely and
replaces it with a buffer of past training levels, each assigned a
learning-potential score. Levels with higher learning potential are
replayed more often. The paper proposes several learning-potential
proxies based on the agent's own value-function statistics — the
best-performing variant uses a rank-weighted estimate derived from
L1 value loss. On the Procgen benchmark suite, Prioritized Level
Replay outperforms uniform sampling, domain randomization, minimax
adversarial training, and Protagonist Antagonist Induced Regret
Environment Design, while using substantially less compute because
it requires only a single reinforcement-learning agent instead of
three.

## Key contributions
- **A learned-adversary-free alternative to Protagonist Antagonist
  Induced Regret Environment Design.** The curriculum signal is
  derived entirely from statistics the agent already produces during
  training — no second agent, no third (environment-generating)
  agent, no additional gradient updates. One reinforcement-learning
  agent total.
- **Learning-potential as a curriculum signal.** Introduces and
  compares several proxies for "how much would additional training
  on this level help the agent": L1 value loss, mean-squared value
  loss, maximum minus minimum return, and others. The L1 value-loss
  variant empirically wins across the Procgen benchmark.
- **Rank-based replay weighting.** Levels are sampled with
  probability proportional to a rank-transformed learning-potential
  score, mixed with a uniform-sampling component to ensure coverage
  of unseen levels. This specific mixing strategy matters and is
  ablated in the paper.
- **Strong empirical results on Procgen.** Beats uniform sampling,
  domain randomization, and Protagonist Antagonist Induced Regret
  Environment Design across 16 procedurally-generated games at
  matched compute. Currently the strongest simple baseline for
  unsupervised environment design in procedural-generation
  benchmarks.
- **Computational efficiency.** Because the method replays past
  levels from a buffer rather than training a generator network,
  the compute cost per step is roughly one-third that of
  Protagonist Antagonist Induced Regret Environment Design (which
  maintains three policies). Sample efficiency per reinforcement-
  learning step is also higher on average.

## Method at a glance
Maintain a buffer of past training levels with associated statistics —
most importantly the most recent value estimates and the observed
returns from the agent's rollouts on each level. Each level has a
priority score that combines:

- A **learning-potential term**, computed from value-function
  statistics on the agent's most recent rollouts of that level
  (L1 value loss is the recommended variant);
- A **staleness term** that increases the priority of levels that
  have not been seen recently.

Levels are sampled with probability proportional to a rank-transformed
combination of these two terms, mixed with a uniform-sampling
fraction. The agent's rollouts on sampled levels update the buffer's
statistics. No separate adversary, no antagonist.

## Why it matters
Prioritized Level Replay is the current simplest-and-strongest
baseline for unsupervised environment design. It demonstrates that
the learned adversary in Protagonist Antagonist Induced Regret
Environment Design is not strictly necessary — the curriculum signal
can be extracted from the agent's own training statistics. This is
operationally important because Protagonist Antagonist Induced Regret
Environment Design is expensive and unstable; Prioritized Level
Replay achieves most of the benefit at a fraction of the cost.
It is also the direct predecessor of Adversarially Compounding
Complexity by Editing Levels, which adds evolutionary mutation to
Prioritized Level Replay's buffer to generate new levels rather than
only replaying old ones.

## Strengths
- **Simplicity.** No learned environment-generator, no antagonist,
  no additional networks. A level buffer plus a priority function
  plus a standard reinforcement-learning inner loop.
- **Strong empirical results at matched compute.** Beats
  Protagonist Antagonist Induced Regret Environment Design across
  Procgen's 16 games, not just on one or two.
- **Clean ablation of learning-potential proxies.** The paper
  compares several candidate signals and identifies the best one
  (L1 value loss, rank-weighted). This makes the method
  tunable and interpretable, unlike Protagonist Antagonist Induced
  Regret Environment Design's adversary whose behavior is opaque.
- **No adversary mode collapse.** The primary failure mode of
  Protagonist Antagonist Induced Regret Environment Design — the
  learned adversary collapsing onto a narrow region of environment
  space — is structurally impossible because there is no learned
  adversary.
- **Naturally composes with other techniques.** Can be combined
  with domain randomization (as a mixing strategy), with mutation-
  based level generation (Adversarially Compounding Complexity by
  Editing Levels builds on this), and with reward-curriculum
  methods.

## Limitations and open critiques
- **Requires a pre-existing pool of levels.** Prioritized Level
  Replay selects from a buffer; it does not *generate* new levels.
  The space of levels has to be parameterised externally (e.g., the
  procedural generator in Procgen, or a level-editor mutation
  operator). For open-ended domains where the environment pool
  should itself grow, Prioritized Level Replay needs to be combined
  with a generator — which is exactly what Adversarially Compounding
  Complexity by Editing Levels does.
- **Learning-potential is a proxy, not regret.** The paper's
  signals (L1 value loss, etc.) are approximations of the true
  learnability gap. In domains where value-loss does not track
  learnability cleanly, the proxy can be misleading.
- **Staleness term needs tuning.** The mix between learning-
  potential priority and staleness-based uniform sampling is
  hyperparameter-sensitive and domain-dependent. Default values
  work on Procgen but may need retuning for new domains.
- **Tested on perfect-information procedural games.** The Procgen
  suite is the primary empirical setting. Whether Prioritized Level
  Replay scales to partially-observable, noisy-reward domains is
  not established by this paper and remains an open empirical
  question for domains like financial trading or real-world robotics.
- **Value-loss signals degrade at training plateau.** When the
  agent is near its ceiling on most levels, value-loss is small
  everywhere and the priority signal becomes uninformative.
  Analogous to the training-curve-shape issue that afflicts any
  learning-potential-based curriculum.

## Follow-up work and dialogue
Prioritized Level Replay is the direct predecessor of
*"Evolving Curricula with Regret-Based Environment Design"*
(Parker-Holder et al., ICML 2022, arXiv:2203.01302) — the
Adversarially Compounding Complexity by Editing Levels paper —
which adds mutation-based level editing on top of Prioritized Level
Replay's priority-based selection. It is also related to Dual
Curriculum Design (Jiang, Dennis, Parker-Holder et al., NeurIPS
2021, arXiv:2110.02439), which generalizes the Protagonist
Antagonist Induced Regret Environment Design game to two curricula
running simultaneously and which Prioritized Level Replay can be a
component of.

In the context of this wiki, Prioritized Level Replay is the
practical alternative to [Protagonist Antagonist Induced Regret
Environment Design](./paired.md) — same target (automatic
curriculum from a pool of environments), simpler machinery, lower
cost, often better empirical performance. A research project that
starts from Protagonist Antagonist Induced Regret Environment Design
and finds itself simplifying toward "antagonist as a non-adversarial
reference policy" is on a trajectory that lands close to Prioritized
Level Replay, which sidesteps the antagonist entirely by using
value-function statistics.

## Reproducibility
- **Open code:** yes, released at the Facebook AI Research
  repository (linked in the paper's introduction).
- **Domains tested:** Procgen benchmark suite (16 procedurally-
  generated 2D games), MiniGrid for comparison with Protagonist
  Antagonist Induced Regret Environment Design.
- **Compute disclosed:** single-agent Proximal Policy Optimization
  runs on Procgen with published hyperparameters; per-game and
  per-method compute is tabulated in the appendix. Prioritized
  Level Replay uses roughly one-third the compute per step that
  Protagonist Antagonist Induced Regret Environment Design does,
  because it maintains one policy instead of three.
- **Hyperparameters:** priority temperature, staleness weight,
  uniform-sampling fraction, buffer size. All reported in the
  paper's experimental section with per-game values.

## When to cite this paper
Cite Prioritized Level Replay as the canonical reference for
*curriculum learning via prioritized replay of past environments*,
using value-function-derived learning-potential as the priority
signal. It is the right citation for any method that replaces a
learned adversary with a buffer-and-priority scheme. For the
mutation-based extension, cite the Adversarially Compounding
Complexity by Editing Levels paper. For the original regret
formulation that Prioritized Level Replay is simplifying, cite
[Protagonist Antagonist Induced Regret Environment Design](./paired.md).

## In the knowledge graph
- **Related concepts:** [unsupervised environment design](../concepts/unsupervised-environment-design.md)
  (Prioritized Level Replay is the dominant alternative to the
  learned-adversary approach of Protagonist Antagonist Induced
  Regret Environment Design),
  [regret as objective](../concepts/regret-as-objective.md)
  (Prioritized Level Replay uses a proxy for regret computed from
  value-function statistics instead of a two-policy gap),
  [automatic curriculum](../concepts/automatic-curriculum.md)
- **Foundations:** [evolutionary computation](../foundations/evolutionary-computation.md)
  (as one of the deep-reinforcement-learning descendants of
  population-based curriculum methods)
- **See also:** [PAIRED](./paired.md) (the learned-adversary
  counterpart Prioritized Level Replay replaces), and —
  conceptually — [MCC](./mcc.md) (whose coevolutionary dual-queue
  dynamic achieves a similar automatic-curriculum effect through
  a population-density gate rather than a value-function proxy).
