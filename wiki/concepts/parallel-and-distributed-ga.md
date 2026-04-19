# Parallel and Distributed Genetic Algorithms

A parallel or distributed Genetic Algorithm (GA) splits the population
across multiple processes or "islands" that evolve mostly
independently, exchanging individuals at controlled intervals. The
island model is the dominant coarse-grained variant.

## Intuition

A canonical [genetic algorithm](../foundations/genetic-algorithms.md) is
embarrassingly parallel at the fitness-evaluation step but inherently
sequential at the selection step: you have to know everyone's fitness
before you can pick parents. Distributing the population across `K`
subpopulations (islands) breaks this constraint. Each island runs its
own GA on its own subpopulation; the only inter-island communication is
periodic *migration*, in which a small fraction of each island's
population is sent to a neighbor.

Two payoffs:

1. **Speedup.** With one island per processor, the per-generation cost
   drops nearly linearly in `K` (Tanese 1989 reported near-linear
   speedup on a 64-processor hypercube; superlinear in some settings).
2. **Diversity preservation.** Each island can converge to a different
   local optimum because migration is rare. Sewall Wright's "shifting
   balance" theory in evolutionary biology is the explicit inspiration:
   small, semi-isolated demes drift independently and occasionally
   exchange material when one finds something useful.

## Mechanics

The island-model loop, per island, is identical to a standard GA except
for the migration step:

```
# Per island i, in parallel:
pop_i = random_population(N // K)
for g in range(num_generations):
    pop_i = ga_step(pop_i)                        # standard GA
    if g % migration_interval == 0:
        emigrants = select_for_migration(pop_i, m)    # m << N/K
        send(neighbor(i), emigrants)
        immigrants = receive(neighbor(i))
        pop_i = replace(pop_i, immigrants)
```

Key parameters:

- **Number of islands `K`.** Determines the parallelism and the level
  of population fragmentation.
- **Subpopulation size `N/K`.** Too small → premature convergence on
  every island; too large → no parallelism gain.
- **Migration interval.** Typical values 5–50 generations. Migration
  every generation is essentially a panmictic GA; never migrating is
  `K` independent runs.
- **Migration rate.** Typical 1–20% of subpopulation per migration
  event. Belding (1995) found ~1% per generation is a sweet spot.
- **Topology.** Ring, torus, fully-connected, or random; each affects
  how fast information propagates between islands.
- **Selection / replacement of migrants.** Best-replace-worst,
  random-replace-random, etc.

A finer-grained variant is the *cellular* or *fine-grained* parallel GA:
each individual sits at a node in a grid, and selection / mating happen
only with grid neighbors. This is parallel-friendly at a different scale
but is not what is usually meant by "island GA".

## Why it works

Two effects dominate.

**Diversity preservation by isolation.** Migration rates of a few
percent per generation are enough to spread useful innovations between
islands but slow enough that each island can independently converge on
different basins of attraction. A panmictic population of the same
total size collapses much faster.

**Reduced selection pressure per evaluation.** Selection in a small
subpopulation amplifies modest fitness differences less than selection
in a large pool, so weakly fit individuals that happen to carry useful
genes survive longer. This connects to the
[novelty / quality-diversity](novelty-and-quality-diversity.md)
intuition that aggressive selection wastes good stepping stones.

The biological grounding (Wright's shifting-balance theory) is
contested in evolutionary biology — Coyne et al. and others have
challenged its empirical relevance — but the algorithmic effect on GA
performance is robust across many studies.

## Trade-offs and failure modes

- **Hyperparameter sensitivity.** Migration interval and rate are
  load-bearing; the same problem can show 2× speedup or no speedup
  depending on tuning.
- **Communication cost.** With many small islands, migration overhead
  can dominate; topology choice matters at scale.
- **Synchronization.** Synchronous migration creates barriers that
  hurt parallel efficiency. Asynchronous migration is common in
  practice but harder to reason about theoretically.
- **Domain dependence.** Whitley, Rana, and Heckendorn (1998) showed
  that island GAs particularly help on linearly separable problems
  where different islands can specialize on different sub-problems.
  On highly non-separable problems the benefit shrinks.
- **Not coevolution.** Despite the name, the islands are *not*
  coevolving — each evolves under the same fitness function, just on a
  different sample. See [coevolution.md](coevolution.md) for the
  contrast.

## Design choices in the literature

- **Tanese 1989** (PhD dissertation; International Conference on
  Genetic Algorithms 1989, ICGA-89) — introduced the Distributed
  Genetic Algorithm (DGA) and demonstrated near-linear speedup on a
  hypercube. The seminal reference.
- **Cohoon, Hegde, Martin, and Richards (1987)** — independent
  introduction of an island-style parallel GA, often cited alongside
  Tanese.
- **Whitley, Rana, and Heckendorn (1998, 1999)** — formal analysis of
  island GAs, especially on separable problems.
- **Belding 1995** ("The Distributed Genetic Algorithm Revisited",
  arXiv:adap-org/9504007) — extended Tanese's experiments to Royal
  Road functions and varied migration parameters; one of the few
  fully-open accessible references in this lineage.
- **Cantú-Paz 1998 and 2000** — the standard survey and the standard
  textbook on parallel GAs.
- **Asynchronous island GAs** — Alba & Tomassini (2002) review.
- **Modern reuse.** [AlphaEvolve](../papers/alphaevolve.md) and
  [ShinkaEvolve](../papers/shinkaevolve.md) both use island
  populations to maintain diversity in their LLM-driven program
  search; ShinkaEvolve cites Tanese 1989 directly.

## Open questions

- Can island topology be learned from problem structure rather than
  hand-picked?
- What is the right migration policy when fitness evaluation is
  extremely expensive (LLM calls, full-program execution)? The
  classical 1%-per-generation heuristic was developed for cheap fitness
  evaluations.
- Does the island model retain its advantage when combined with strong
  variation operators (CMA-ES, LLM mutation), or does it become
  redundant?
- Is there an equivalent of the island model for LLM-driven evolution
  that exploits the LLM's *internal* multi-modal generation rather than
  external population-level diversity?

## Papers that exemplify this

- [Island GA](../papers/island-ga.md) — Belding's accessible
  arXiv reference for the canonical distributed/island model;
  cites Tanese 1989 as the seminal predecessor.
- [AlphaEvolve](../papers/alphaevolve.md) — uses island populations of
  evolved programs to preserve diversity during LLM-driven program
  search.
- [ShinkaEvolve](../papers/shinkaevolve.md) — uses island
  subpopulations seeded from the same initial program with occasional
  migration; explicitly cites Tanese 1989.

## Related wiki pages

- [../foundations/genetic-algorithms.md](../foundations/genetic-algorithms.md)
- [../foundations/evolutionary-computation.md](../foundations/evolutionary-computation.md)
- [novelty-and-quality-diversity.md](novelty-and-quality-diversity.md)
- [llm-driven-evolution.md](llm-driven-evolution.md)
