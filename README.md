# Evolutionary Search — Research Wiki

This repository organizes knowledge about evolutionary search as a
navigable knowledge graph: classical genetic algorithms and their
parallel/distributed variants, coevolution, open-ended evolution,
unsupervised environment design (UED), and the modern thread of
LLM-driven program evolution. It moves from general concepts (what
evolutionary computation is, what coevolution means, why open-endedness
matters, what changes when an LLM becomes the variation operator) down
through cross-cutting technical ideas, all the way to the individual
research papers that sit as leaves of the graph. The goal is to make
it easy to locate a paper inside a broader intellectual context, to
compare methods that drive emergent complexity through population
interaction or LLM-guided code mutation, and to understand how the
field connects classical neuroevolution (NEAT, novelty search, MCC) and
distributed GAs (Tanese, island models) with modern deep RL approaches
to adversarial environment generation (PAIRED) and with the new
LLM-evolution lineage (FunSearch, AlphaEvolve, ShinkaEvolve).

## Structure

- `papers/` — PDF leaves. One file per primary research paper, named by
  slug and identifier (for example `paired_2012.02096.pdf`,
  `mcc_brant-stanley_2017.pdf`, `alphaevolve_2506.13131.pdf`). These
  are the empirical ground truth the wiki points at.
- `wiki/` — the markdown knowledge graph. Top-level folders are
  `foundations/`, `concepts/`, and `papers/`. Every page links up to its
  parent section and across to related nodes so that a reader can walk
  the graph rather than hunt through a flat list.

## Start here

- [wiki/overview.md](wiki/overview.md) — the wiki entry point and map of
  the territory.
- [wiki/index.md](wiki/index.md) — flat catalog of every wiki page with
  a one-line gloss; the first stop when you know a topic but not where
  it lives.
- [wiki/log.md](wiki/log.md) — chronological append-only record of
  ingests, queries-filed-back, lint passes, refactors, and schema
  changes.
- [wiki/papers/papers.md](wiki/papers/papers.md) — index of the paper
  leaves, with one short page per paper.

## Section hubs

Every wiki section has a folder-note hub named after the section itself.
The full set, in reading order:

- [wiki/foundations/foundations.md](wiki/foundations/foundations.md) —
  classical genetic algorithms and evolutionary computation, the
  intellectual ancestors of every page in the wiki.
- [wiki/concepts/concepts.md](wiki/concepts/concepts.md) — cross-cutting
  technical ideas: coevolution, open-ended evolution, minimal criterion,
  novelty / quality-diversity, unsupervised environment design, regret as
  objective, automatic curriculum, parallel and distributed GA,
  LLM-driven evolution.
- [wiki/papers/papers.md](wiki/papers/papers.md) — the paper leaves.

## Scope (as of 2026-04)

Version 0 of the wiki covers seven seed papers that span the six
lineages the wiki is built around:

- [AlphaZero](wiki/papers/alphazero.md) — Silver et al., DeepMind
  Science 2018 — self-play coevolution at scale; single
  algorithm mastering chess, shogi, and Go from random play;
  canonical reference for self-play as automatic curriculum.
- [Island GA](wiki/papers/island-ga.md) — Belding 1995 (ICGA),
  with Tanese 1989 and Whitley/Rana/Heckendorn 1998 cited as the
  seminal and theoretical references — the classical
  parallel/distributed GA branch.
- [MCC](wiki/papers/mcc.md) — Brant & Stanley, GECCO 2017 —
  neuroevolution framing, dual-population coevolution under a minimal
  criterion, the canonical reference for archive-free open-ended
  coevolution.
- [PAIRED](wiki/papers/paired.md) — Dennis et al., NeurIPS 2020 —
  deep RL framing, regret-maximizing environment adversary, the
  canonical reference for unsupervised environment design.
- [AlphaEvolve](wiki/papers/alphaevolve.md) — Novikov et al., Google
  DeepMind 2025 — scaled-up LLM-driven program evolution; demonstrated
  novel algorithmic discoveries including a Strassen improvement on
  4×4 complex matrices.
- [ShinkaEvolve](wiki/papers/shinkaevolve.md) — Lange, Imajuku, Cetin,
  Sakana AI 2025 — open-source, sample-efficient counterpart to
  AlphaEvolve; power-law parent sampling, code novelty rejection, and
  bandit-based LLM ensemble selection.
- [Model Merging](wiki/papers/model-merging.md) — Akiba, Shing, Tang,
  Sun, Ha, Sakana AI 2024 — evolutionary search over merging recipes
  for pretrained neural networks (parameter-space + data-flow-space
  joint optimization with CMA-ES); the "evolve weights" counterpart
  to AlphaEvolve / ShinkaEvolve's "evolve code".

Together they outline the six main lineages — distributed GA,
neuroevolutionary open-ended coevolution, deep-RL adversarial
curricula, self-play coevolution, LLM-driven program evolution, and
evolutionary merging of trained weights — that the rest of the wiki
is structured to grow into. Additions are welcome via pull
request; please follow the existing page layout and link conventions.

## Credits and license

PDFs in `papers/` are reproduced under their original licenses
(arXiv preprints, ACM published proceedings); copyright remains with the
respective authors. The wiki text (everything under `wiki/`, and this
README) is released under CC-BY-4.0.

## Related wiki pages

- [wiki/overview.md](wiki/overview.md)
- [wiki/concepts/concepts.md](wiki/concepts/concepts.md)
