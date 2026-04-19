# Coevolution

Coevolution is any setting — in Evolutionary Computation (EC) or its
modern deep-learning descendants — where the fitness of an individual
depends on other simultaneously evolving individuals, either within
one population (self-play) or across two or more interacting
populations.

## Intuition

Plain [Genetic Algorithms (GAs)](../foundations/genetic-algorithms.md)
score each candidate against a fixed objective. Coevolution removes the
"fixed" assumption: the thing that defines fitness is itself moving.
The classic image is a predator-prey arms race, but the practically
important versions are about pairing learners with tests. If you cannot
write down a useful difficulty curve, perhaps you can let two
populations write it for each other: tests that no learner can solve
are uninformative; tests that every learner solves are uninformative;
the interesting tests are exactly the ones that current learners barely
miss. Coevolution can, in principle, generate those tests automatically.

The taxonomic split inherited from the EC literature is competitive vs
cooperative coevolution:

- **Competitive.** Individuals are rewarded for beating other
  individuals (predator/prey, attacker/defender, agent/environment).
- **Cooperative.** Individuals are rewarded for performing well as part
  of a team (subcomponent decomposition).

Both PAIRED and MCC sit *outside* this dichotomy, in interesting ways.

## Mechanics

The minimal coevolutionary loop is:

```
pop_A, pop_B = init_populations()
while not converged:
    for a in sample(pop_A):
        for b in sample(pop_B):
            score_a, score_b = interact(a, b)        # joint evaluation
            update_fitness(a, score_a)
            update_fitness(b, score_b)
    pop_A = evolve(pop_A)
    pop_B = evolve(pop_B)
```

The interesting design choices are:

- **Pairing policy.** All-vs-all is `O(N²)` and rarely affordable.
  Common alternatives: round-robin against a sample, against an archive
  of past champions (Hall of Fame), or against the current opponent
  population only.
- **Reward symmetry.** In pure competition, `score_a + score_b = 0`
  (zero-sum). In MCC, neither population's fitness is a function of the
  other's score at all; only the binary "did the interaction satisfy a
  minimal criterion" matters. In PAIRED, the adversary's reward is the
  *gap* between two protagonists' scores —
  [regret](regret-as-objective.md), not raw outcome.
- **Population separation.** Two distinct populations vs one population
  playing itself (self-play). Self-play is a degenerate case of
  coevolution and is dominant in modern game-playing Reinforcement
  Learning (RL), e.g. AlphaZero.

## Why it works

When the fitness landscape is hard to specify in closed form but easy
to *probe* by interaction, coevolution lets the algorithm discover the
landscape and the solution simultaneously. The same dynamic that
produces predator-prey arms races in nature can, with care, produce
[automatic curricula](automatic-curriculum.md): difficulty schedules
that the engineer never writes down because the second population
maintains the right level of challenge as the first improves.

The trick is "with care". Competitive coevolution has well-known
pathologies (next section) that have made it look promising for decades
without delivering robust results outside narrow domains. The two seed
papers in this wiki are interesting precisely because they sidestep the
classical pathologies — PAIRED via regret, MCC via the
[minimal criterion](minimal-criterion.md) — rather than confronting
them head-on.

## Trade-offs and failure modes

The coevolution literature documents a small zoo of failure modes that
any new method has to defend against:

- **Loss of gradient.** If population A is too strong, every interaction
  with B is a win — there is no fitness gradient for B. Symmetric
  problem when B is too strong.
- **Mediocre stable states.** Both populations reach an equilibrium in
  which neither has any incentive to improve, even though absolute
  performance is poor. Ficici & Pollack (1998) is the classical
  reference.
- **Cyclic dynamics.** Strategies cycle (rock → paper → scissors → rock)
  rather than improving; the algorithm chases its tail.
- **Coevolutionary forgetting.** Population A solves test b₁, then
  evolves to solve b₂, then b₃, by which point it can no longer solve
  b₁ — a real problem when the goal is generalization.
- **Disengagement.** One population finds an irrelevant niche and stops
  interacting meaningfully with the other.

Mitigations include Hall-of-Fame archives, Pareto coevolution
(De Jong 2004), [novelty pressure](novelty-and-quality-diversity.md),
genetics-based speciation, and dropping the explicit reward in favor of
a [minimal criterion](minimal-criterion.md).

## Design choices in the literature

- **Symmetric self-play** ([AlphaZero](../papers/alphazero.md),
  OpenAI Five) — one population, zero-sum game, Monte Carlo Tree
  Search or RL inside the loop.
- **Asymmetric two-population competition** — Hillis (1990) coevolved
  sorting networks against test cases; the modern descendant is
  adversarial-test coevolution in software testing.
- **Cooperative coevolution** (Potter & De Jong 2000) — decompose a
  large problem into subcomponents, evolve a population per subcomponent.
- **Minimal Criterion Coevolution** ([MCC](../papers/mcc.md)) — drop
  fitness ranking, keep only a binary "did this interaction satisfy
  the Minimal Criterion (MC)" check. Closer to a viability filter
  than a competition.
- **Regret-driven environment design** ([PAIRED](../papers/paired.md))
  — adversary's reward is the gap between protagonist and antagonist;
  by construction, the adversary is incentivized to produce *solvable*
  hard environments rather than impossible ones.
- **Coevolutionary curricula in deep RL** — Paired Open-Ended
  Trailblazer (POET; Wang et al. 2019), Enhanced POET (Wang et
  al. 2020), and Adversarially Compounding Complexity by Editing
  Levels (ACCEL; Parker-Holder et al. 2022) all coevolve agents and
  environments in some form. Most of these reuse the
  [open-ended evolution](open-ended-evolution.md) framing
  popularized by the neuroevolution community.

## Open questions

- When is two-population coevolution preferable to single-population
  self-play? The literature has no clean answer.
- Can coevolution avoid the loss-of-gradient problem in
  high-dimensional deep RL the way regret-based methods claim to,
  outside the narrow MiniGrid setting where PAIRED was demonstrated?
- Is genetics-based speciation (MCC speciated variant) a reliable
  diversity preserver or a domain-specific trick?
- How does LLM-driven evolution interact with coevolutionary dynamics —
  if both populations are programs and the LLM mutates both, do the
  classical pathologies re-emerge or transform?

## Papers that exemplify this

- [MCC](../papers/mcc.md) — dual-population coevolution of mazes and
  NEAT-evolved navigators under a binary minimal criterion.
- [PAIRED](../papers/paired.md) — coevolution of an RL protagonist,
  an RL antagonist, and a learned environment-generating adversary,
  with regret as the shared objective.
- [AlphaZero](../papers/alphazero.md) — symmetric self-play
  coevolution at scale; MCTS-augmented policy iteration on game
  self-play data; the cleanest demonstration that self-play alone
  produces an automatic curriculum.

## Related wiki pages

- [open-ended-evolution.md](open-ended-evolution.md)
- [minimal-criterion.md](minimal-criterion.md)
- [regret-as-objective.md](regret-as-objective.md)
- [automatic-curriculum.md](automatic-curriculum.md)
- [unsupervised-environment-design.md](unsupervised-environment-design.md)
- [../foundations/evolutionary-computation.md](../foundations/evolutionary-computation.md)
