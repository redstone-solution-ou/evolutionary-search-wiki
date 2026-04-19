# Regret as Objective

Regret is the gap between the return obtained by a particular policy
on a particular task and the return that an optimal (or proxy-optimal)
policy would have obtained on the same task. Using regret as a training
signal — instead of raw return — changes which environments and tasks
the algorithm chooses to focus on.

## Intuition

Plain Reinforcement Learning (RL) maximizes expected return. Plain
minimax adversarial RL minimizes the worst-case return. Both rules push the system toward
extremes — easy environments under maximization, impossible
environments under minimax — that produce poor learning signal in
opposite ways. Regret is the in-between rule: pick the environment
where the *gap* between current behavior and optimal behavior is
largest. By construction, this rule prefers environments that are
solvable (so optimal behavior achieves something) but not yet solved
by the current policy (so the gap is wide). That is exactly the
"learnable" frontier.

In classical decision theory under ignorance, this corresponds to
Savage's (1951) minimax regret rule, and PAIRED's framing makes the
identification explicit.

## Mechanics

For a policy `π`, environment parameter `θ`, and return function `U`:

```
REGRET(π, θ) = max_π* U_θ(π*) - U_θ(π)
```

The optimal policy `π*` is generally not available, so PAIRED
approximates it with an *antagonist* policy `π_A` trained alongside the
protagonist `π_P`:

```
REGRET_PAIRED(π_P, π_A, θ) ≈ U_θ(π_A) - U_θ(π_P)
```

The environment adversary's reward is `+REGRET`; the protagonist's
reward is `-REGRET` (= raw return, after rearrangement); the
antagonist's reward is `+REGRET`. The full Protagonist Antagonist
Induced Regret Environment Design (PAIRED) loop:

```
loop:
    θ ~ adversary_policy(π_P, π_A)
    τ_P = rollout(env(θ), π_P)
    τ_A = rollout(env(θ), π_A)
    r_regret = U_θ(τ_A) - U_θ(τ_P)
    rl_update(π_P, reward=-r_regret)
    rl_update(π_A, reward=+r_regret)
    rl_update(adversary, reward=+r_regret)
```

A practical refinement (PAIRED Section 4): use
`max_{τ_A} U_θ(τ_A) - mean_{τ_P} U_θ(τ_P)` to reduce noise — the best
antagonist trajectory minus the average protagonist trajectory.

## Why it works

The minimax-regret decision rule has a property neither maximin nor
domain randomization has. PAIRED's Theorem 1: under reasonable
assumptions about reward structure (success rewards strictly dominate
failure rewards, and the spread within each class is smaller than the
gap between classes), if there exists *any* policy that succeeds on
all solvable environments, minimax regret will choose such a policy.
Maximin and uniform randomization can both fail this property.

The intuitive reason: regret zeroes out on unsolvable environments
(both protagonist and antagonist fail equally). The adversary therefore
has no incentive to produce them. Regret also zeroes out on already-
mastered environments (both protagonists succeed equally). The
adversary's only profitable region is "solvable but not yet solved" —
the learning frontier.

PAIRED's Theorem 2 closes the loop: at Nash equilibrium of the
three-player game, the protagonist's policy is exactly the minimax-regret
policy.

## Trade-offs and failure modes

- **Antagonist as oracle is approximate.** The theorems assume the
  antagonist achieves the optimal return on every Θ. In practice, the
  antagonist is also an RL learner and can be weaker than the
  protagonist on some Θ — turning the regret signal into noise. PAIRED's
  Appendix E.1 explores variants that relax this.
- **Three-policy training cost.** Three RL learners is more expensive
  than one. PLR (Jiang et al. 2021) showed that replaying past
  high-regret levels from a buffer can replace the learned adversary
  while preserving most of the benefit, at lower cost.
- **Regret estimation noise.** Both `U_θ(π_A)` and `U_θ(π_P)` are
  Monte Carlo estimates. The regret signal has variance proportional to
  both. Multi-rollout averaging helps but not arbitrarily.
- **Regret-based selection is myopic.** It picks environments that
  maximize *current-step* learning signal, not long-term curriculum
  progress. POET-style population-based methods can produce better
  long-horizon trajectories at the cost of more compute.

## Design choices in the literature

- **PAIRED** ([papers/paired.md](../papers/paired.md)) — antagonist
  policy as the proxy oracle; learned adversary as the environment
  policy.
- **Prioritized Level Replay (PLR)** (Jiang et al. 2021) — no learned
  adversary; sample past levels with probability proportional to
  estimated regret (Temporal Difference error or value-loss as proxy).
  Often outperforms PAIRED on procedurally-generated benchmarks.
- **ACCEL** (Parker-Holder et al. 2022) — combine PLR's regret-based
  level selection with mutation-based level editing. The "evolutionary
  curriculum" branch of UED.
- **Robust adversarial RL with regret** — older bandit and robust-MDP
  literature (Regan & Boutilier 2010, Ghavamzadeh et al. 2016) used
  regret minimization for safe policy improvement; analytical methods
  that did not scale to deep RL.

## Open questions

- What is the right regret estimator when the antagonist is much
  weaker than the protagonist? Negative regret has no clean meaning in
  the PAIRED objective.
- Can regret be replaced by a learned "level value" that does not
  require the antagonist at all? PLR is one answer but is also myopic.
- Does regret-driven selection compose with off-policy RL methods
  (Q-learning, soft actor-critic) the way it does with PPO?
- Is regret the right primitive in the LLM era, where "the optimal
  policy" is partly defined by what an LLM can do, and the antagonist
  could itself be an LLM?

## Papers that exemplify this

- [PAIRED](../papers/paired.md) — the canonical reference: regret as
  the adversary's reward, antagonist as the proxy oracle.

## Related wiki pages

- [unsupervised-environment-design.md](unsupervised-environment-design.md)
- [automatic-curriculum.md](automatic-curriculum.md)
- [coevolution.md](coevolution.md)
- [../foundations/evolutionary-computation.md](../foundations/evolutionary-computation.md)
