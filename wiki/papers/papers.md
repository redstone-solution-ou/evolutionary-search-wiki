# Papers

The leaf layer of the wiki. One short page per primary research paper,
linked from the relevant concept and foundation pages. Every claim on a
leaf cites a section, table, or figure of the paper itself; for
cross-paper synthesis, see the concept pages.

## Index

| Slug | Title | Authors | Year | Venue | Lineage |
|------|-------|---------|------|-------|---------|
| [alphaevolve](alphaevolve.md) | AlphaEvolve: A Coding Agent for Scientific and Algorithmic Discovery | Novikov, Vũ, Eisenberger et al. | 2025 | Google DeepMind white paper | LLM-driven program evolution |
| [island-ga](island-ga.md) | The Distributed Genetic Algorithm Revisited | Belding | 1995 | ICGA-95 | parallel / distributed GA |
| [mcc](mcc.md) | Minimal Criterion Coevolution: A New Approach to Open-Ended Search | Brant, Stanley | 2017 | GECCO | neuroevolution / open-ended |
| [paired](paired.md) | Emergent Complexity and Zero-shot Transfer via Unsupervised Environment Design | Dennis, Jaques, Vinitsky et al. | 2020 | NeurIPS | deep RL / UED |
| [shinkaevolve](shinkaevolve.md) | ShinkaEvolve: Towards Open-Ended and Sample-Efficient Program Evolution | Lange, Imajuku, Cetin | 2025 | Sakana AI tech report | LLM-driven program evolution |

## Grouping by lineage

**Classical parallel evolutionary computation.** Papers that parallelize
the canonical GA over subpopulations with periodic migration; the
structural ancestor of every later population-structure choice in this
wiki.

- [Island GA](island-ga.md) — Belding 1995 revisits Tanese's
  Distributed Genetic Algorithm on Royal Road benchmarks; canonical
  reference list (Tanese 1989, Whitley/Rana/Heckendorn 1998) lives in
  the leaf body.

**Neuroevolutionary open-ended coevolution.** Papers that use
evolutionary algorithms (typically NEAT-derived) to coevolve agents
and tasks under non-objective constraints; the curriculum is a side
effect of mutual viability.

- [MCC](mcc.md) — dual-queue coevolution of NEAT-evolved maze
  navigators and procedurally evolved mazes under a binary minimal
  criterion.

**Deep-RL adversarial environment design.** Papers that train an RL
agent in environments produced by another learned process; the
curriculum is a side effect of the adversarial loop.

- [PAIRED](paired.md) — environment adversary maximizes regret between
  protagonist and antagonist agents; produces solvable, increasingly
  difficult environments.

**LLM-driven program evolution.** Papers that keep the EA
selection-mutation-evaluation loop but use a large language model as
the variation operator over source code.

- [AlphaEvolve](alphaevolve.md) — Google DeepMind's scaled-up
  successor to FunSearch; full-file evolution with SOTA LLM ensemble,
  hours-long evaluators, and demonstrated novel algorithmic
  discoveries.
- [ShinkaEvolve](shinkaevolve.md) — Sakana AI's open-source,
  sample-efficient counterpart; introduces power-law parent sampling,
  code-embedding novelty rejection, and bandit-based LLM ensemble
  selection.

## Related wiki pages

- [../concepts/concepts.md](../concepts/concepts.md)
- [../foundations/foundations.md](../foundations/foundations.md)
- [../overview.md](../overview.md)
