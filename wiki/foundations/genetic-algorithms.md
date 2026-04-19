# Genetic Algorithms

A genetic algorithm (GA) is a population-based search procedure that
maintains a set of candidate solutions, evaluates each by a fitness
function, and produces the next generation by selection, crossover, and
mutation.

## Intuition

A GA treats the search space as a population of genomes (typically
fixed-length bitstrings or real-valued vectors). At every generation,
high-fitness individuals are preferentially picked as parents, their
genomes are recombined and perturbed, and the offspring replace some
fraction of the parent population. The algorithm has no model of the
fitness landscape — only the ability to sample it. The hope is that
useful "building blocks" (short, low-order, high-fitness schemata) are
preserved by selection, recombined by crossover, and propagated through
the population over generations. Holland's schema theorem (1975) is the
classical theoretical underpinning of this hope, though the modern view
treats GAs as one stochastic optimizer among many.

## Mechanics

A canonical generational GA loop:

```
population = random_population(N)
while not converged:
    fitnesses = [evaluate(x) for x in population]
    parents = selection(population, fitnesses)        # e.g. tournament
    children = []
    for p1, p2 in pairs(parents):
        c1, c2 = crossover(p1, p2, rate=p_c)          # e.g. 1-point
        c1 = mutation(c1, rate=p_m)                   # e.g. bit-flip
        c2 = mutation(c2, rate=p_m)
        children += [c1, c2]
    population = replacement(population, children)    # e.g. elitism
```

Key knobs: population size `N`, crossover rate `p_c`, mutation rate
`p_m`, selection pressure (tournament size, truncation fraction), and
replacement scheme (generational vs steady-state, with or without
elitism). Each makes a different trade-off between exploration
(maintaining diversity, sampling new regions) and exploitation
(converging on the current best).

## Why it works

A GA is most useful when:

- The fitness function is cheap to evaluate but the landscape is rugged
  (many local optima), so gradient-based methods get stuck.
- The genome encoding has *some* useful locality: small mutations or
  crossovers between fit parents tend to produce reasonable children.
- The "building block" structure is real — partial good solutions can
  be recombined into better ones rather than destroyed.

When those conditions fail, a GA degenerates to random search with extra
steps. Modern continuous-domain practice often prefers
[evolution strategies](evolutionary-computation.md) (CMA-ES) or
gradient-based optimizers; GAs remain dominant in combinatorial,
discrete, or program-structured domains.

## Trade-offs and failure modes

- **Premature convergence.** Strong selection pressure plus low
  diversity collapses the population onto a local optimum, after which
  mutation alone is too weak to escape. Mitigations include speciation,
  crowding, restart, or
  [parallel/distributed populations](../concepts/parallel-and-distributed-ga.md).
- **Hitchhiking and deceptive landscapes.** Schemata that share genome
  positions with truly fit ones get amplified even when neutral or
  harmful. Tanese-style functions are the classical pathological case.
- **Bloat.** In variable-length GAs (notably genetic programming),
  genomes grow without producing fitness gains.
- **Hyperparameter sensitivity.** Crossover and mutation rates,
  population size, and selection scheme interact strongly; "tuning the
  GA" is itself a research subfield.

## Where this leads

The wiki organizes its modern descendants along three axes that all
modify the canonical loop above:

- **Drop the fitness function.** [Novelty search and quality diversity](../concepts/novelty-and-quality-diversity.md)
  replace fitness with a behavioral-novelty signal; the
  [minimal criterion](../concepts/minimal-criterion.md) replaces it with
  a binary viability check.
- **Couple two populations.** [Coevolution](../concepts/coevolution.md)
  evaluates each individual against members of another evolving
  population, which can produce arms races, automatic curricula, or
  [open-ended evolution](../concepts/open-ended-evolution.md).
- **Replace mutation with an LLM.**
  [LLM-driven evolution](../concepts/llm-driven-evolution.md) keeps the
  selection-mutation-evaluation loop but uses a large language model as
  the variation operator over code.

## Open questions

- When does the building-block hypothesis actually hold for problems we
  care about, versus being an explanatory fiction?
- How do we predict the right population size and selection pressure
  ahead of time, rather than tuning by trial and error?
- Are there principled ways to switch between exploration and
  exploitation phases dynamically?
- What is the relationship between GAs and modern gradient-based
  optimization on the same problems?

## Papers that exemplify this

- [MCC](../papers/mcc.md) — uses a GA over maze-wall genomes alongside a
  NEAT-evolved neural-network population; the GA template is intact, only
  the fitness function is replaced by a binary minimal criterion.
- [Island GA](../papers/island-ga.md) — distributes the canonical GA
  across subpopulations with periodic migration; the per-island loop is
  textbook GA.
- [AlphaEvolve](../papers/alphaevolve.md) and
  [ShinkaEvolve](../papers/shinkaevolve.md) — keep selection, mutation,
  and evaluation, but the genome is a program and the mutation operator
  is an LLM.

## Related wiki pages

- [evolutionary-computation.md](evolutionary-computation.md)
- [../concepts/coevolution.md](../concepts/coevolution.md)
- [../concepts/parallel-and-distributed-ga.md](../concepts/parallel-and-distributed-ga.md)
- [../concepts/llm-driven-evolution.md](../concepts/llm-driven-evolution.md)
