# Unsupervised Environment Design

Unsupervised Environment Design (UED) is the problem of automatically
producing a useful distribution of environments to train a
Reinforcement Learning (RL) agent in, given only an *underspecified*
environment with free parameters and no hand-designed difficulty
curve.

## Intuition

Many RL problems require designing the training environment carefully:
specifying obstacles, choosing parameter ranges, picking a curriculum.
Doing this by hand is error-prone and labor-intensive, and it does not
adapt to the agent's current skill level. UED reframes the problem.
The developer provides a parametrized environment template — a
"underspecified POMDP" with some free parameters Θ — and a UED algorithm
produces a distribution over Θ that the agent should train against.

The formal contribution of [PAIRED](../papers/paired.md) is to define
UED precisely (Section 3 of Dennis et al.) and to position it as a
*decisions-under-ignorance* problem from classical decision theory:
each existing approach corresponds to a known decision rule.

| Approach | Environment policy | Decision rule |
|---|---|---|
| Domain Randomization (DR) | uniform over Θ | insufficient reason (Laplace) |
| Minimax adversarial | argmin over Θ of agent return | maximin (Wald) |
| Protagonist Antagonist Induced Regret Environment Design (PAIRED) | regret-maximizing Θ | minimax regret (Savage) |

## Mechanics

The UED template is:

```
M = underspecified_environment(free_params=Θ)
π = init_policy()
while not converged:
    θ_dist = environment_policy(π)        # this is the UED algorithm
    θ ~ θ_dist
    rollout = collect(M(θ), π)
    π = rl_update(π, rollout)
```

The single design choice is `environment_policy`: how to map the
current agent policy to a distribution over environment parameters.

- **Domain randomization** ignores `π` and returns the uniform
  distribution. Cheap, but never adapts to the agent's level — easy
  environments dominate forever.
- **Minimax** returns the worst-case Θ for the current policy. Adapts
  but is incentivized to produce *unsolvable* environments (impossible
  mazes), which provide no learning signal.
- **PAIRED** trains a learned environment generator (the "adversary")
  whose reward is *regret*: the gap between the protagonist's return
  and an antagonist's return on the same Θ. Unsolvable Θ have zero
  regret (both protagonists fail equally) and are therefore
  disincentivized; the adversary's optimum is the *easiest* Θ on which
  the protagonist fails but the antagonist succeeds. See
  [regret-as-objective.md](regret-as-objective.md).

The downstream effect is an [automatic curriculum](automatic-curriculum.md):
as the protagonist improves, the adversary must find harder Θ to keep
producing positive regret, and difficulty rises naturally with skill.

## Why it works

UED works when the underspecified environment is *expressive enough*
to contain a continuum of difficulties and when the agent's skill is
*identifiable* — that is, the regret signal can distinguish "agent
cannot do this yet" from "no agent can do this". When both hold, the
adversary's gradient pushes Θ exactly into the
"zone of proximal development" (PAIRED Section 4 explicitly invokes
Vygotsky's term).

Theorem 1 of Dennis et al. is the load-bearing theoretical claim: under
mild assumptions about reward structure, minimax regret will choose a
policy that succeeds on every solvable Θ — a property neither domain
randomization nor minimax adversarial training has. Theorem 2 shows
that at Nash equilibrium of the protagonist/antagonist/adversary game,
the protagonist plays the minimax regret policy.

## Trade-offs and failure modes

- **Computational cost.** PAIRED requires training three policies
  simultaneously (protagonist, antagonist, adversary). Each adversary
  step generates a fresh environment that has to be solved by both
  protagonists — the per-step compute is 3× a baseline RL run, before
  counting environment-generator updates.
- **Adversary collapse.** The adversary can fail to learn — generating
  uniform noise — in which case PAIRED degenerates to domain
  randomization. PAIRED's appendix discusses regret-approximation
  variants designed to mitigate this.
- **Domain expressiveness.** All published PAIRED experiments use
  MiniGrid-style maze tasks where the parameter space is small and
  combinatorial. Whether UED scales to high-dimensional continuous
  parameter spaces (terrain, robot morphology, MuJoCo physics) is an
  open empirical question; later work — Replay-Guided Adversarial
  Environment Design (PLR), POET, ACCEL — explored this.
- **Antagonist coordination assumption.** Theorem 2 requires the
  antagonist and adversary to be jointly optimal against the
  protagonist; in practice they are RL-trained and approximate this
  only loosely. Appendix E.1 reports alternative approximations.

## Design choices in the literature

- **Domain randomization** (Tobin et al. 2017) — sample environment
  parameters uniformly. The "no UED" baseline.
- **Minimax adversarial training** (Pinto et al. 2017 and others) — an
  adversary minimizes the protagonist's return. Predates UED framing.
- **POET** (Wang et al. 2019, 2020) — a population of (agent,
  environment) pairs evolved together; agents transfer between
  environments under a manually-tuned reward threshold. POET is a
  population-based predecessor of PAIRED.
- **PAIRED** ([papers/paired.md](../papers/paired.md)) — the
  formalization plus the regret-based instantiation.
- **Prioritized Level Replay (PLR) / Replay-Guided ACED** (Jiang et
  al. 2021) — drops the learned adversary in favor of replaying
  high-regret levels from a buffer; often more sample-efficient than
  PAIRED.
- **ACCEL** (Parker-Holder et al. 2022) — combines PLR with
  evolutionary editing of past levels to generate new ones.

## Open questions

- Does UED actually scale beyond MiniGrid-style maze domains? Most
  positive results remain in low-dimensional combinatorial spaces.
- Is regret the right adversary objective, or is "level value" / "level
  replay priority" (PLR) a more practical proxy?
- How does UED interact with the underlying RL algorithm (PPO, SAC,
  IMPALA)? PAIRED uses PPO; the regret signal interacts non-trivially
  with on-policy advantage estimation.
- Can the same UED machinery be applied to LLM training environments
  (e.g., adversarially generated math problems for an RL fine-tuning
  loop)?

## Papers that exemplify this

- [PAIRED](../papers/paired.md) — the formalization of UED and the
  canonical regret-based instantiation.

## Related wiki pages

- [regret-as-objective.md](regret-as-objective.md)
- [automatic-curriculum.md](automatic-curriculum.md)
- [coevolution.md](coevolution.md)
- [open-ended-evolution.md](open-ended-evolution.md)
- [../foundations/evolutionary-computation.md](../foundations/evolutionary-computation.md)
