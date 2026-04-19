# Emergent Complexity and Zero-shot Transfer via Unsupervised Environment Design

> **Short name:** `paired` · **arXiv:** [2012.02096](https://arxiv.org/abs/2012.02096) · **PDF:** [local](../../papers/paired_2012.02096.pdf) · **Date:** 2020-12 · **Venue:** NeurIPS 2020

**Authors:** Michael Dennis, Natasha Jaques, Eugene Vinitsky et al. (UC Berkeley BAIR + Google Brain)

## Abstract
A wide range of RL problems — robustness, transfer, unsupervised RL,
emergent complexity — require specifying a distribution of training
environments. The authors propose Unsupervised Environment Design (UED)
as a paradigm: developers provide an *underspecified* environment with
free parameters, and an algorithm produces a distribution over fully-
specified, solvable environments tailored to the agent. They introduce
PAIRED (Protagonist Antagonist Induced Regret Environment Design), in
which an environment-generating adversary is allied with a second,
*antagonist* agent; the adversary is rewarded for the regret between
protagonist and antagonist returns. Experiments on MiniGrid mazes show
PAIRED produces a natural curriculum and yields agents with stronger
zero-shot transfer than domain randomization or minimax adversarial
training.

## Key contributions
- Formalization of [Unsupervised Environment Design](../concepts/unsupervised-environment-design.md)
  as a problem class, with the Underspecified POMDP (UPOMDP) as the
  formal object (Section 3).
- Identification of UED with classical decisions-under-ignorance:
  domain randomization ↔ insufficient reason, minimax ↔ Wald's
  maximin, PAIRED ↔ Savage's [minimax regret](../concepts/regret-as-objective.md).
- The PAIRED algorithm itself: a three-player game between
  protagonist π_P, antagonist π_A, and environment adversary
  Λ̃, with regret = U_θ(π_A) − U_θ(π_P) as the shared signal
  (Algorithm 1, Section 4).
- Two theorems: (Th. 1) minimax regret chooses success-preserving
  policies under mild reward assumptions; (Th. 2) at Nash equilibrium
  of the PAIRED game, the protagonist plays the minimax regret policy.
- Empirical demonstration on MiniGrid maze navigation showing that
  PAIRED produces an [automatic curriculum](../concepts/automatic-curriculum.md)
  of solvable but increasingly hard mazes and that PAIRED-trained
  agents transfer zero-shot to novel hand-designed mazes that defeat
  domain-randomized and minimax-trained baselines.

## Method at a glance
PAIRED maintains three RL learners. At each step, the adversary samples
environment parameters θ; the protagonist and antagonist each roll out
in env(θ); the adversary's reward is the *difference* between the
antagonist's best trajectory and the protagonist's mean trajectory
return. The protagonist receives the negative of regret as its reward
(equivalent to its own return, after rearrangement). All three learners
are trained with PPO and share no parameters.

## Why it matters
PAIRED gave UED a theoretical home in classical decision theory and a
practical algorithm that demonstrably escapes the failure modes of
both domain randomization (no curriculum) and minimax adversarial
training (unsolvable environments). It became the canonical reference
point for the line of work that includes POET, Prioritized Level
Replay (PLR), ACCEL, and dual-curriculum design — the modern
"adversarial environment generation" branch of curriculum RL.

## Strengths
- Clean theoretical framing tying UED to a classical decision-theoretic
  rule (minimax regret), with explicit success guarantees (Theorem 1)
  that minimax adversarial training and domain randomization
  demonstrably do not have.
- The protagonist–antagonist construction *constructively* solves the
  unsolvable-environment problem of minimax training: regret zeroes out
  on environments no policy can solve, removing the adversary's
  incentive to produce them.
- The MiniGrid experiments report transfer to a suite of held-out
  hand-designed mazes the agent never saw during training, providing
  a real (rather than in-distribution) generalization signal.
- The algorithm is agnostic to the underlying RL method and to the
  parameterization of the environment; the same PAIRED loop ports
  cleanly to different domains.

## Limitations and open critiques
- All published experiments are in MiniGrid-style mazes where the
  parameter space Θ is small and combinatorial. Whether PAIRED scales
  to high-dimensional continuous environment spaces (procedurally
  generated terrain, rich physics, robot morphology) is left open by
  this paper and remains contested in the follow-up literature.
- Three-player RL training is computationally heavier than a baseline
  RL run — three policies, two protagonist rollouts per environment,
  plus the adversary update. Sample-efficiency at the level of
  protagonist environment steps is not reported relative to a
  domain-randomization baseline matched on wall-clock or sample count.
- The antagonist-as-oracle assumption underlying Theorem 1 (the
  antagonist achieves optimal return on every Θ) is not satisfied
  empirically; the antagonist is an RL learner that approximates
  optimality only loosely. Appendix E.1 reports variants that relax
  this and gives empirical results, but the theorem's conditions are
  not directly verifiable in the experimental runs.
- The adversary can collapse — generating uninformative environments
  if its own RL signal is weak — degrading PAIRED to domain
  randomization. The paper discusses this risk but does not provide
  diagnostic tools to detect it during training.

## Follow-up work and dialogue
The dominant follow-up is Prioritized Level Replay (PLR; Jiang et al.
2021), which drops the learned adversary in favor of a buffer that
replays past high-regret levels. PLR is cheaper, often as good or
better, and is the de facto baseline for procedurally-generated
benchmarks. ACCEL (Parker-Holder et al. 2022) combines PLR with
mutation-based level editing — bringing PAIRED's adversary back as an
*evolutionary* level generator rather than an RL one. Dual Curriculum
Design (Jiang et al. 2021) generalizes the PAIRED game to two
curricula running simultaneously. POET (Wang et al. 2019; Enhanced
POET 2020) is the population-based predecessor that PAIRED's Section 2
explicitly contrasts itself with. On the conceptual side, PAIRED is
often paired with [MCC](./mcc.md) as the two endpoints of the
"second population drives complexity" idea — PAIRED in deep RL with
explicit regret, MCC in neuroevolution with a binary
[minimal criterion](../concepts/minimal-criterion.md).

## Reproducibility
- **Open code:** yes — released at
  `github.com/google-research/google-research/tree/master/social_rl/`
  (cited in Section 4 footnote).
- **Domains used:** MiniGrid maze navigation (one main domain), with
  specific transfer mazes hand-designed for evaluation. No
  high-dimensional or continuous-control experiments.
- **Compute disclosed:** training duration in environment steps is
  reported in the experimental section; explicit GPU-hours and FLOPs
  are not given.
- **Hyperparameters:** PPO hyperparameters and PAIRED-specific
  parameters (number of antagonist rollouts, adversary update
  frequency) are reported in the appendix.

## When to cite this paper
Cite PAIRED as the canonical reference for the formalization of
Unsupervised Environment Design and for the regret-driven adversary
construction. It is also the right citation for the
decision-theoretic mapping (UED ↔ decisions under ignorance) and for
Theorem 1 / Theorem 2. For practical curriculum-RL implementations,
PLR or ACCEL are usually preferred; cite PAIRED for the conceptual
foundation and PLR / ACCEL for the empirical recipe.

## In the knowledge graph
- **Related concepts:** [unsupervised environment design](../concepts/unsupervised-environment-design.md),
  [regret as objective](../concepts/regret-as-objective.md),
  [automatic curriculum](../concepts/automatic-curriculum.md),
  [coevolution](../concepts/coevolution.md)
- **Foundations:** [evolutionary computation](../foundations/evolutionary-computation.md)
  (the deep-RL descendant of population-based adversarial training)
- **See also:** [MCC](./mcc.md) (the neuroevolutionary counterpart with
  a binary minimal criterion in place of regret)
