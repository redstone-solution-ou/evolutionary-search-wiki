# Foundations

Two pages live in this section. They describe the algorithmic
machinery that every later page in the wiki implicitly modifies, and
they are the right place to start if "Genetic Algorithm" or
"evolution strategy" is not already a familiar phrase.

## The canonical loop, and why it matters

A [Genetic Algorithm (GA)](genetic-algorithms.md) maintains a
population of candidate solutions, scores each by a *fitness*
function, picks parents in proportion to fitness, recombines and
perturbs them by *crossover* and *mutation*, and replaces some
fraction of the population with the resulting children. That four-verb
loop — population, selection, variation, evaluation — is the spine
of every method covered elsewhere in the wiki. The
[GA page](genetic-algorithms.md) writes the loop down explicitly,
walks through its hyperparameters, and lists its classical failure
modes (premature convergence, hitchhiking, hyperparameter
sensitivity) so that the rest of the wiki can talk about *which knob
each modern descendant turns* without redefining the knobs each time.

Reading this page first lets the [concept layer](../concepts/concepts.md)
make sense as a register of edits to a known template.
[Minimal Criterion Coevolution (MCC)](../papers/mcc.md) becomes "drop
fitness ranking; replace it with a binary viability check; couple two
populations". [LLM-driven evolution](../concepts/llm-driven-evolution.md)
becomes "keep selection; replace mutation and crossover with calls to
a Large Language Model". [Island GAs](../concepts/parallel-and-distributed-ga.md)
become "keep the loop; run several copies in parallel; let them
exchange individuals occasionally". Without the canonical loop in mind
those phrasings sound like vocabulary salad; with it, they are
precise.

## The broader Evolutionary Computation family

The textbook GA is one member of a larger family. The
[Evolutionary Computation (EC)](evolutionary-computation.md) page maps
that family — Evolution Strategies (notably the
[Covariance Matrix Adaptation Evolution Strategy (CMA-ES)](../concepts/cma-es.md),
the dominant continuous-domain black-box optimizer), Genetic
Programming for tree-structured programs, Estimation-of-Distribution
Algorithms, and the neuroevolution branch which is the immediate
ancestor of [MCC](../papers/mcc.md) via NeuroEvolution of Augmenting
Topologies (NEAT; Stanley & Miikkulainen 2002), the canonical
algorithm that grows neural networks from minimal structure.

The page also names the two bridges that the rest of the wiki crosses
repeatedly: the *EC-to-deep-RL* bridge, where population-based methods
meet Reinforcement Learning, and the *EC-to-LLM* bridge, where the
mutation operator becomes a Large Language Model. Reading those two
sections is enough to see why such different-looking papers as
[PAIRED](../papers/paired.md) (deep RL plus an environment adversary)
and [AlphaEvolve](../papers/alphaevolve.md) (LLMs plus an island
model) end up indexed under the same wiki.

## Where to go next

- The [concepts hub](../concepts/concepts.md) is the next layer up:
  one page per modification of the canonical loop. Read it after these
  two foundation pages and the design space becomes visible.
- The [papers hub](../papers/papers.md) lists the leaves grouped by
  lineage, with a one-line gloss per paper.
- The [overview](../overview.md) tells the same story end-to-end as a
  single linear narrative across all four lineages.

## Pages in this section

- [genetic-algorithms.md](genetic-algorithms.md) — the canonical
  population / selection / crossover / mutation / fitness loop, with
  pseudocode and the classical failure modes.
- [evolutionary-computation.md](evolutionary-computation.md) — the
  broader EC umbrella (Evolution Strategies, Genetic Programming,
  neuroevolution and NEAT, Estimation-of-Distribution Algorithms),
  plus the bridges to deep RL and LLM-driven evolution.

## Related wiki pages

- [../concepts/coevolution.md](../concepts/coevolution.md) — the
  central organizing modification of the loop in this wiki.
- [../concepts/llm-driven-evolution.md](../concepts/llm-driven-evolution.md) —
  the modern thread, where the variation operator becomes an LLM.
- [../concepts/parallel-and-distributed-ga.md](../concepts/parallel-and-distributed-ga.md) —
  the island model, the structural primitive that reappears across
  decades.
- [../papers/papers.md](../papers/papers.md) — the paper leaves.
