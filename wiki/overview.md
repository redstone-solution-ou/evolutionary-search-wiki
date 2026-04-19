# Knowledge Wiki — Evolutionary Search

This wiki is a hand-curated knowledge graph about evolutionary search:
classical genetic algorithms (GAs), coevolution, open-ended evolution,
unsupervised environment design (UED), and the modern thread of
large-language-model (LLM) driven program evolution. It is organized general-to-specific: high-level
orientation pages sit at the top, cross-cutting concepts in the middle,
and individual research papers form the leaves. Each page is short,
links forward and backward, and ends with a list of related nodes so
that readers can move through the graph rather than reading it
linearly.

The wiki pairs with the `papers/` directory at the repo root, which
holds the actual PDFs. A typical traversal starts here, narrows down
through [concepts/coevolution.md](concepts/coevolution.md),
[concepts/unsupervised-environment-design.md](concepts/unsupervised-environment-design.md),
or [concepts/llm-driven-evolution.md](concepts/llm-driven-evolution.md)
to the right framing, clicks into a related concept page for context,
and finally opens the corresponding leaf in `papers/` to read the paper
itself. Cross-links are always relative paths, so the wiki is fully
browsable on GitHub or offline.

## First stop at query time

- [index.md](index.md) — flat catalog of every wiki page with a
  one-line gloss; the schema-mandated first stop when answering a
  question.
- [log.md](log.md) — chronological append-only record of ingests,
  queries-filed-back, lint passes, refactors, and schema changes.

## Start here as a researcher

If you arrived here wanting to learn the area, read these in order:

- [foundations/genetic-algorithms.md](foundations/genetic-algorithms.md) —
  the classical GA template that every page on this wiki implicitly
  modifies (selection, mutation, crossover, fitness).
- [foundations/evolutionary-computation.md](foundations/evolutionary-computation.md) —
  the broader EC family (evolution strategies, neuroevolution, GP),
  where coevolution sits in the taxonomy, and the bridges to deep RL
  and LLMs.
- [concepts/coevolution.md](concepts/coevolution.md) — the central
  organizing idea of the coevolution branch of the wiki: what changes
  when you let two populations evolve against each other.
- [concepts/parallel-and-distributed-ga.md](concepts/parallel-and-distributed-ga.md) —
  the island model: a structural primitive that started as a
  parallelism trick and now appears in modern LLM-evolution systems.
- [concepts/open-ended-evolution.md](concepts/open-ended-evolution.md) —
  why "no overarching objective" is a feature and what it costs.
- [concepts/minimal-criterion.md](concepts/minimal-criterion.md) — the
  simplest non-objective constraint, the engine of [MCC](papers/mcc.md).
- [concepts/unsupervised-environment-design.md](concepts/unsupervised-environment-design.md) —
  the modern deep-RL framing in which the environment itself is the
  thing being optimized, formalized by [PAIRED](papers/paired.md).
- [concepts/regret-as-objective.md](concepts/regret-as-objective.md) —
  the decision-theoretic move that distinguishes PAIRED from minimax
  adversarial training and from domain randomization.
- [concepts/llm-driven-evolution.md](concepts/llm-driven-evolution.md) —
  the modern thread: the EA loop with an LLM as the variation
  operator, demonstrated by [AlphaEvolve](papers/alphaevolve.md) and
  [ShinkaEvolve](papers/shinkaevolve.md).
- [concepts/automatic-curriculum.md](concepts/automatic-curriculum.md) —
  the shared output of the coevolutionary and adversarial-RL branches:
  a difficulty schedule the algorithm produces rather than the
  engineer hand-codes.
- [papers/papers.md](papers/papers.md) — the paper index, with one
  short page per leaf.

## Reading paths

- **15-minute overview.** Read
  [foundations/genetic-algorithms.md](foundations/genetic-algorithms.md),
  then [concepts/coevolution.md](concepts/coevolution.md) and
  [concepts/llm-driven-evolution.md](concepts/llm-driven-evolution.md),
  then skim the paper index [papers/papers.md](papers/papers.md).
- **Compare the coevolution lineages.** Read
  [concepts/coevolution.md](concepts/coevolution.md) for the shared
  framing, then walk through [papers/mcc.md](papers/mcc.md) (the
  neuroevolution / open-ended branch) and
  [papers/paired.md](papers/paired.md) (the deep-RL / regret branch),
  noting how each instantiates "second population drives complexity".
- **Compare the LLM-driven evolution papers.** Read
  [concepts/llm-driven-evolution.md](concepts/llm-driven-evolution.md)
  for the shared template, then
  [papers/alphaevolve.md](papers/alphaevolve.md) and
  [papers/shinkaevolve.md](papers/shinkaevolve.md), noting the
  closed-vs-open and scale-vs-sample-efficiency contrasts.
- **Trace the population-structure thread.** Start at
  [papers/island-ga.md](papers/island-ga.md), follow forward to
  [papers/alphaevolve.md](papers/alphaevolve.md) and
  [papers/shinkaevolve.md](papers/shinkaevolve.md) to see how the
  island model from 1989 reappears in 2025 LLM systems.
- **Pick a method for a use case.** Curriculum for a deep RL agent →
  [PAIRED](papers/paired.md). Open-ended artifact generation without
  a pre-defined fitness → [MCC](papers/mcc.md). Parallelize an
  existing GA → [Island GA](papers/island-ga.md). Use an LLM to evolve
  a piece of code → [AlphaEvolve](papers/alphaevolve.md) for scale,
  [ShinkaEvolve](papers/shinkaevolve.md) for sample efficiency or
  open-source.

## Top-level sections

- [foundations/](foundations/foundations.md) — classical genetic
  algorithms and the broader evolutionary computation family that the
  modern literature builds on.
- [concepts/](concepts/concepts.md) — cross-cutting technical ideas:
  [coevolution](concepts/coevolution.md),
  [open-ended evolution](concepts/open-ended-evolution.md),
  [minimal criterion](concepts/minimal-criterion.md),
  [novelty and quality diversity](concepts/novelty-and-quality-diversity.md),
  [unsupervised environment design](concepts/unsupervised-environment-design.md),
  [regret as objective](concepts/regret-as-objective.md),
  [automatic curriculum](concepts/automatic-curriculum.md),
  [parallel and distributed GA](concepts/parallel-and-distributed-ga.md),
  [LLM-driven evolution](concepts/llm-driven-evolution.md).
- [papers/](papers/papers.md) — the paper leaves.

## Knowledge-graph sketch

```
evolutionary-search-wiki
|
+-- foundations/
|   +-- genetic-algorithms     (selection, mutation, crossover, fitness)
|   +-- evolutionary-computation (ES, GP, neuroevolution, NEAT, bridges)
|
+-- concepts/
|   +-- coevolution            ----> MCC, PAIRED
|   +-- open-ended-evolution   ----> MCC, ShinkaEvolve
|   +-- minimal-criterion      ----> MCC
|   +-- novelty-and-quality-diversity (novelty search, MAP-Elites)
|   +-- unsupervised-environment-design (UED) ----> PAIRED
|   +-- regret-as-objective    ----> PAIRED
|   +-- automatic-curriculum   ----> MCC, PAIRED
|   +-- parallel-and-distributed-ga ----> Island GA, AlphaEvolve, ShinkaEvolve
|   +-- llm-driven-evolution   ----> AlphaEvolve, ShinkaEvolve
|
+-- papers/
    +-- island-ga    (Belding 1995, ICGA / cites Tanese 1989, Whitley 1998)
    +-- mcc          (Brant & Stanley 2017, GECCO)
    +-- paired       (Dennis et al. 2020, NeurIPS)
    +-- alphazero    (Silver et al. 2017/2018, Science)
    +-- alphaevolve  (Novikov et al. 2025, DeepMind)
    +-- shinkaevolve (Lange et al. 2025, Sakana AI)
```

## Related wiki pages

- [concepts/coevolution.md](concepts/coevolution.md)
- [concepts/llm-driven-evolution.md](concepts/llm-driven-evolution.md)
- [concepts/unsupervised-environment-design.md](concepts/unsupervised-environment-design.md)
- [papers/papers.md](papers/papers.md)
