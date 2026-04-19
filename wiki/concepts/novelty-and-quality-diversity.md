# Novelty and Quality Diversity

Novelty search and quality diversity (QD) are a family of
non-objective evolutionary algorithms that reward behavioral
divergence — explicitly or implicitly — instead of, or in addition to,
optimizing a fitness function.

## Intuition

In a deceptive landscape, "always climb toward the objective" is a
losing strategy: the path to the objective requires going through
intermediate states that look bad. Lehman & Stanley's
"Abandoning Objectives" (2011) argued that this is not a fixable bug
in objective-driven search — it is the central reason objective search
fails on hard problems. Their alternative was novelty search: replace
the fitness function with a measure of how *behaviorally different*
each individual is from everything seen before.

Quality diversity (QD) adds back a quality signal but keeps the
divergence pressure. The output is no longer a single best individual
but a *collection* of high-quality individuals spread across a
behavior space — a repertoire of solutions, each occupying a different
niche.

## Mechanics

**Novelty search** maintains an archive of past behaviors. Each new
individual is scored by its mean distance in behavior space to its k
nearest archive neighbors:

```
novelty(x) = mean_k_nearest(behavior(x), archive)
```

Selection uses this novelty score in place of fitness. Periodically,
new individuals are added to the archive (typically when their novelty
exceeds a threshold).

**MAP-Elites** (Mouret & Clune 2015) discretizes the behavior space
into a grid. Each cell stores the highest-fitness individual whose
behavior falls in that cell. New individuals are inserted into the
cell their behavior maps to, displacing the current occupant only if
their fitness is higher:

```
grid = empty_grid(behavior_dimensions)
while not done:
    parent = sample_grid(grid)
    child = mutate(parent)
    cell = behavior_to_cell(behavior(child))
    if grid[cell] is empty or fitness(child) > fitness(grid[cell]):
        grid[cell] = child
```

The result is a map showing the best solution found *for each
behavioral niche* — explicitly multi-modal output instead of a single
optimum.

**Novelty search with local competition (NSLC)** (Lehman & Stanley
2011) sits between the two: novelty drives exploration, a local
competition score breaks ties within behavioral neighborhoods.

## Why it works

The diagnosis is that fitness-based selection wastes search effort
re-evaluating points near the current best, while novelty-based
selection forces the search to spread. In deceptive problems, spreading
is exactly what is needed to escape the local optima that fitness
gradients lead into.

Empirically, novelty search and MAP-Elites repeatedly outperform pure
fitness optimization on hard exploration problems (deceptive mazes,
robot morphology design, soft-robot control). The catch is that they
require a behavior characterization (BC) — a hand-designed function
that maps a phenotype to a low-dimensional behavior descriptor — and
the choice of BC is deeply load-bearing.

## Trade-offs and failure modes

- **Behavior characterization is hand-designed.** Picking the right BC
  is the new fitness-function problem. The MCC paper explicitly cites
  this dependence as a key motivation for replacing novelty pressure
  with the [minimal criterion](minimal-criterion.md).
- **Behavior space is finite.** Once the archive has covered the BC
  space, novelty pressure decays. Soros et al. (2017) and others have
  proposed BC expansion mechanisms, but none has become standard.
- **Computational cost.** The archive grows; nearest-neighbor lookups
  become expensive at scale.
- **Quality vs diversity trade-off.** Pure novelty discards quality
  information. MAP-Elites' grid is one fix but introduces grid-design
  choices.

## Design choices in the literature

- **Novelty search** (Lehman & Stanley 2008, 2011) — replace fitness
  with novelty; archive past behaviors.
- **MCNS — minimal criterion novelty search** (Lehman & Stanley 2010) —
  add a viability filter; an immediate ancestor of MCC.
- **NSLC — novelty search with local competition** (Lehman & Stanley
  2011) — novelty for selection, local fitness for tie-breaking.
- **MAP-Elites** (Mouret & Clune 2015) — discretized behavior grid,
  one elite per cell. Now the dominant QD method in robotics.
- **CVT-MAP-Elites** — Voronoi tessellation in continuous behavior
  spaces.
- **MCC** ([papers/mcc.md](../papers/mcc.md)) — explicitly frames
  itself against this lineage: keep the divergence-preserving spirit
  but drop the BC, the archive, and the explicit novelty score.

## Where this leads

The LLM-driven evolution thread takes a different angle on
"diversity preservation". [ShinkaEvolve](../papers/shinkaevolve.md)
implements a *code novelty rejection sampler*: candidate programs are
embedded with a learned encoder and rejected if cosine similarity to
the archive exceeds a threshold. This is novelty search reimplemented
on top of an LLM mutation operator — the BC is replaced by a learned
embedding, the archive remains.

## Open questions

- Can BCs be learned automatically from the search trajectory itself?
- When is the MAP-Elites grid the right discretization, and when does
  it impose harmful structure?
- How much of the success of novelty methods is due to "exploration is
  good" rather than "novelty is good in particular"?
- Can LLM embeddings serve as a domain-general BC for code or
  configuration evolution?

## Papers that exemplify this

- [MCC](../papers/mcc.md) — methodological foil; argues that coevolution
  + minimal criterion can replace novelty + archive.
- [ShinkaEvolve](../papers/shinkaevolve.md) — adopts novelty
  rejection-sampling on top of an LLM mutation operator.

## Related wiki pages

- [open-ended-evolution.md](open-ended-evolution.md)
- [minimal-criterion.md](minimal-criterion.md)
- [coevolution.md](coevolution.md)
- [llm-driven-evolution.md](llm-driven-evolution.md)
- [../foundations/evolutionary-computation.md](../foundations/evolutionary-computation.md)
