# Papers

The leaf layer of the wiki. One short page per primary research paper,
linked from the relevant concept and foundation pages. Every claim on a
leaf cites a section, table, or figure of the paper itself; for
cross-paper synthesis, see the concept pages.

## Index

| Slug | Title | Authors | Year | Venue | Lineage |
|------|-------|---------|------|-------|---------|
| [alphaevolve](alphaevolve.md) | AlphaEvolve: A Coding Agent for Scientific and Algorithmic Discovery | Novikov, Vũ, Eisenberger et al. | 2025 | Google DeepMind white paper | LLM-driven program evolution |
| [alphazero](alphazero.md) | Mastering Chess and Shogi by Self-Play with a General Reinforcement Learning Algorithm | Silver, Hubert, Schrittwieser et al. | 2017/2018 | arXiv / Science | self-play coevolution |
| [grammatical-evolution](grammatical-evolution.md) | Grammatical Evolution | O'Neill, Ryan | 2001 | IEEE Transactions on Evolutionary Computation 5(4) | grammar-constrained EA |
| [island-ga](island-ga.md) | The Distributed Genetic Algorithm Revisited | Belding | 1995 | ICGA-95 | parallel / distributed GA |
| [lmx](lmx.md) | Language Model Crossover: Variation through Few-Shot Prompting | Meyerson, Nelson, Bradley, Gaier, Moradi, Hoover, Lehman | 2023 / 2024 | arXiv / ACM TELO | LLM-driven evolution (foundational) |
| [mcc](mcc.md) | Minimal Criterion Coevolution: A New Approach to Open-Ended Search | Brant, Stanley | 2017 | GECCO | neuroevolution / open-ended |
| [model-merging](model-merging.md) | Evolutionary Optimization of Model Merging Recipes | Akiba, Shing, Tang, Sun, Ha | 2024 | Sakana AI (arXiv, later Nature Machine Intelligence) | evolutionary merging of trained weights |
| [paired](paired.md) | Emergent Complexity and Zero-shot Transfer via Unsupervised Environment Design | Dennis, Jaques, Vinitsky et al. | 2020 | NeurIPS | deep RL / UED |
| [shinkaevolve](shinkaevolve.md) | ShinkaEvolve: Towards Open-Ended and Sample-Efficient Program Evolution | Lange, Imajuku, Cetin | 2025 | Sakana AI tech report | LLM-driven program evolution |

## Grouping by lineage

**Self-play coevolution.** Papers where an agent coevolves against
copies of itself, producing an automatic curriculum from the
self-play dynamic. The cleanest and most widely-cited branch of
coevolution in the modern deep-RL era.

- [AlphaZero](alphazero.md) — Silver et al. 2017/2018. Neural
  network plus Monte Carlo Tree Search, trained entirely from
  self-play, single algorithm mastering chess, shogi, and Go from
  random play.

**Grammar- / schema-constrained evolutionary search.** Papers that
evolve candidates required to satisfy a formal grammar or schema —
the non-LLM foundational approach to evolving structured outputs
(programs, configurations, DSL expressions, JSON / YAML documents).

- [Grammatical Evolution](grammatical-evolution.md) — O'Neill &
  Ryan, IEEE TEC 2001. Genotype is a variable-length integer
  string; phenotype is produced by decoding the genotype through a
  user-supplied BNF grammar (or any other formal grammar, including
  JSON Schema). Genetic operators act on the integer genome; the
  grammar guarantees syntactic validity of every offspring. Serves
  as the classical, non-LLM counterpart to LMX-style LLM-driven
  variation over structured text.

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
the variation operator over source code or any text-serialisable
genotype.

- [LMX — Language Model Crossover](lmx.md) — Meyerson et al.,
  2023 / ACM TELO 2024. The foundational paper: a pretrained LLM,
  prompted with several parent examples, naturally produces outputs
  that behave like evolutionary crossover, across arbitrary
  text-serialisable genotypes (code, configs, JSON, sentences,
  math expressions, image prompts). The theoretical foundation for
  the two systems below and for evolving JSON / YAML / DSL
  representations generally.
- [AlphaEvolve](alphaevolve.md) — Google DeepMind's scaled-up
  successor to FunSearch; full-file evolution with SOTA LLM ensemble,
  hours-long evaluators, and demonstrated novel algorithmic
  discoveries.
- [ShinkaEvolve](shinkaevolve.md) — Sakana AI's open-source,
  sample-efficient counterpart; introduces power-law parent sampling,
  code-embedding novelty rejection, and bandit-based LLM ensemble
  selection.

**Evolutionary merging of trained weights.** Papers that treat the
weights of pretrained neural networks as the genome and evolve
merging recipes arithmetically, without gradient-based training per
offspring. Complementary to the LLM-driven-code lineage above.

- [Model Merging](model-merging.md) — Akiba et al., Sakana AI 2024.
  Evolves merging recipes in parameter space (weight interpolation
  coefficients, searched with CMA-ES) and data flow space (which
  layers from which source model appear in the composite).
  Demonstrates cross-domain capability transfer; notably produces a
  Japanese Math LLM by merging a Japanese LLM with an English math
  LLM.

## Related wiki pages

- [../concepts/concepts.md](../concepts/concepts.md)
- [../foundations/foundations.md](../foundations/foundations.md)
- [../overview.md](../overview.md)
