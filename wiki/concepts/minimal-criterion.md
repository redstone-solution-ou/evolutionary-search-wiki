# Minimal Criterion

A Minimal Criterion (MC) is a binary, non-ranked viability constraint
used in place of a fitness function: an individual either satisfies
the MC and is allowed to reproduce, or does not and is removed. There
is no comparison between satisfying individuals.

## Intuition

Most evolutionary algorithms select parents in proportion to fitness:
better individuals are more likely to reproduce. The minimal criterion
flips this. There is no "better"; there is only "viable". As long as
an individual clears the bar, it is in the reproductive pool, and any
viable individual is as eligible as any other. The bar can be trivial
(survive long enough to reproduce, in nature) or meaningful (solve at
least one maze, in MCC), but it is never a ranking.

The reason to bother is divergence preservation. Fitness-based
selection produces convergence by construction: the population
concentrates on the current best. A binary viability filter does not.
The population drifts, exploring the viable region without being pulled
toward any single point. If the viable region is itself shifting
(because a [coevolving](coevolution.md) partner population is also
moving), drift across that shifting region produces an
[open-ended](open-ended-evolution.md) stream of qualitatively new
solutions.

## Mechanics

The MCC instantiation, from Brant & Stanley (2017):

```
queue_A = bootstrap(novelty_search, n_seeds=20)   # initial viable agents
queue_B = bootstrap(novelty_search, n_seeds=10)   # initial viable mazes

while True:
    parents_A = queue_A.dequeue(batch_size)
    children_A = reproduce(parents_A)             # NEAT mutation
    queue_A.enqueue(parents_A)                    # parents return
    for child in children_A:
        if satisfies_MC(child, queue_B):          # solves at least one maze in B
            queue_A.enqueue(child)
    if queue_A.size > capacity:
        queue_A.remove_oldest(queue_A.size - capacity)

    # symmetric step for queue_B
```

Three features distinguish this from a standard GA loop:

1. **No fitness ranking.** The selection signal is one bit per child.
2. **Queue-based selection.** Parents are drawn in insertion order,
   not by fitness. Every viable individual gets at least one chance to
   reproduce.
3. **MC depends on the other population.** `satisfies_MC(child_A,
   queue_B)` interrogates queue B — coevolution is built into the MC
   itself, not bolted on as an interaction step.

## Why it works

Three intertwined reasons.

**It avoids the "tricky art of crafting an objective function"** that
Viability Evolution (ViE; Maesani et al. 2014) and earlier minimal-criterion
work explicitly cite as a motivation. You only need to specify what
counts as alive, not what counts as better.

**It keeps the reproductive pool large.** Selecting only the top
individuals discards stepping stones — partial solutions that look bad
under the objective but lie on the path to good ones. Viability filters
keep them.

**Combined with [coevolution](coevolution.md), it produces a moving
target without explicit difficulty schedules.** As population A
improves, individuals in B that A could not solve become accessible;
new mazes evolve to push past the current frontier; the MC tightens
implicitly. This is the engine behind MCC's
[automatic curriculum](automatic-curriculum.md).

## Trade-offs and failure modes

- **MC choice is load-bearing.** Soros & Stanley (2014) argued a
  *non-trivial* MC is necessary for open-endedness; if the MC is too
  easy, the population drifts in a useless direction. If too hard, the
  bootstrap fails and nothing reproduces. Picking the MC is the new
  version of the "design the fitness function" problem, only easier
  because it is binary.
- **No partial credit.** A solution that almost satisfies the MC is
  treated identically to one that does not. This is the cost of the
  divergence-preserving binary signal.
- **Convergence is still possible.** MCC's Section 5 results show that
  the unspeciated control variant converges to similar maze topologies
  across runs; speciation was needed to maintain divergence. The MC
  alone is not sufficient.
- **Does not work without coupled populations.** Drift inside a fixed
  viable region eventually exhausts it. The MC engine needs a
  coevolutionary partner to keep moving the region.

## Design choices in the literature

- **Viability Evolution** (Mattiussi & Floreano 2003; Maesani et
  al. 2014) — single-population MC with shrinking viability boundaries
  that *do* converge over time. The original MC method.
- **Minimal Criterion Novelty Search (MCNS)** (Lehman & Stanley 2010) —
  novelty search with an MC filter to prune useless lineages.
- **Chromaria** (Soros & Stanley 2014) — alife world where the MC is
  satisfied through interaction with other organisms; the conceptual
  ancestor of MCC.
- **MCC** ([papers/mcc.md](../papers/mcc.md)) — drops the shrinking
  viability boundary and the novelty signal entirely; uses only a fixed
  binary MC plus dual-population coevolution.

## Open questions

- Is there a principled way to choose the MC, or is it always domain
  craft?
- Can the MC itself coevolve with the populations, removing the last
  hand-engineered piece?
- Does MC selection compose with gradient-based inner loops? All
  published MC work uses neuroevolution; deep Reinforcement Learning
  (RL) has not been tried.
- Why does MCC need genetics-based speciation to maintain divergence
  across runs? The MC alone should be enough in principle.

## Papers that exemplify this

- [MCC](../papers/mcc.md) — the canonical reference: dual-population
  MC with no fitness ranking, no novelty archive, no behavior
  characterization.

## Related wiki pages

- [coevolution.md](coevolution.md)
- [open-ended-evolution.md](open-ended-evolution.md)
- [novelty-and-quality-diversity.md](novelty-and-quality-diversity.md)
- [automatic-curriculum.md](automatic-curriculum.md)
- [../foundations/genetic-algorithms.md](../foundations/genetic-algorithms.md)
