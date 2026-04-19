# Wiki Log

Append-only chronological record of work on this wiki. Newest at the
bottom. Each entry uses a consistent prefix so the log is parseable
with simple tools (`grep "^## \[" log.md | tail -10`).

## [2026-04-19] scaffold | Initial repo created

Created the repository skeleton following the karpathy LLM-wiki pattern,
adapting the [ts-forecasting-wiki](https://github.com/) layout to the
broader topic of evolutionary search (genetic algorithms, coevolution,
open-ended evolution, unsupervised environment design, and LLM-driven
program evolution). Three layers in place: `papers/` (immutable PDFs),
`wiki/` (LLM-owned markdown), and `CLAUDE.md` (schema). Sections
instantiated: `foundations/`, `concepts/`, `papers/`. Five seed leaves
ingested in the same scaffold pass — see entries below.

## [2026-04-19] scope | Repo originally named coevolution-wiki, renamed

Initially scaffolded as `coevolution-wiki` for the two seed papers
PAIRED and MCC. Renamed to `evolutionary-search-wiki` after the user
expanded the scope to include Island GA, AlphaEvolve, and ShinkaEvolve,
which together span much more than coevolution proper. Internal
references and CLAUDE.md identity updated accordingly.

## [2026-04-19] ingest | PAIRED (arXiv:2012.02096)

Added Dennis et al., "Emergent Complexity and Zero-shot Transfer via
Unsupervised Environment Design", NeurIPS 2020. Created
[papers/paired.md](papers/paired.md). Touched concept pages
[unsupervised-environment-design.md](concepts/unsupervised-environment-design.md),
[regret-as-objective.md](concepts/regret-as-objective.md),
[automatic-curriculum.md](concepts/automatic-curriculum.md), and
[coevolution.md](concepts/coevolution.md). Connected to the
[evolutionary-computation.md](foundations/evolutionary-computation.md)
foundation page as the deep-RL descendant of population-based
adversarial training.

## [2026-04-19] ingest | MCC (Brant & Stanley, GECCO 2017)

Added Brant & Stanley, "Minimal Criterion Coevolution: A New Approach
to Open-Ended Search", GECCO 2017. Created
[papers/mcc.md](papers/mcc.md). Touched concept pages
[minimal-criterion.md](concepts/minimal-criterion.md),
[open-ended-evolution.md](concepts/open-ended-evolution.md),
[novelty-and-quality-diversity.md](concepts/novelty-and-quality-diversity.md),
[coevolution.md](concepts/coevolution.md), and
[automatic-curriculum.md](concepts/automatic-curriculum.md).
Cross-linked to [genetic-algorithms.md](foundations/genetic-algorithms.md)
(NEAT inherits the GA selection-mutation template) and to
[evolutionary-computation.md](foundations/evolutionary-computation.md)
(neuroevolution branch).

## [2026-04-19] ingest | Island GA (Belding 1995, arXiv:adap-org/9504007)

Added Belding, "The Distributed Genetic Algorithm Revisited", ICGA-95.
Created [papers/island-ga.md](papers/island-ga.md). The leaf cites
Tanese (1989) as the seminal DGA reference and Whitley/Rana/Heckendorn
(1998) as the standard theoretical analysis; Belding is included as
the leaf because it is openly accessible on arXiv. Created the new
concept page
[parallel-and-distributed-ga.md](concepts/parallel-and-distributed-ga.md)
to host the island-model material. Cross-linked to
[genetic-algorithms.md](foundations/genetic-algorithms.md) and
[evolutionary-computation.md](foundations/evolutionary-computation.md).
Added forward links from the island-model concept page to
[AlphaEvolve](papers/alphaevolve.md) and
[ShinkaEvolve](papers/shinkaevolve.md), which both reuse the island
model.

## [2026-04-19] ingest | AlphaEvolve (arXiv:2506.13131)

Added Novikov et al., "AlphaEvolve: A coding agent for scientific and
algorithmic discovery", Google DeepMind 2025. Created
[papers/alphaevolve.md](papers/alphaevolve.md). Created the new concept
page [llm-driven-evolution.md](concepts/llm-driven-evolution.md) to
host the LLM-as-mutation pattern. Cross-linked from
[parallel-and-distributed-ga.md](concepts/parallel-and-distributed-ga.md)
(AlphaEvolve uses island populations from FunSearch) and from
[open-ended-evolution.md](concepts/open-ended-evolution.md). Updated
[evolutionary-computation.md](foundations/evolutionary-computation.md)
"bridge to LLMs" section.

## [2026-04-19] query-filed-back | CMA-ES covariance update, PCA analogy, CMA-ME

Chat conversation on "what is the covariance matrix in CMA-ES / CMA-ME
— is it a PCA over all the population?" was non-trivial and surfaced a
gap: the wiki had no dedicated page on CMA-ES and the CMA-ME treatment
lived in a single bullet inside novelty-and-quality-diversity.md.
Filed back as the new concept page
[cma-es.md](concepts/cma-es.md), covering the sampling distribution
`Normal(m, σ²C)`, the rank-μ (PCA-like, weighted-over-top-μ
offspring) and rank-1 (evolution-path / EMA) covariance updates, the
CSA step-size rule, the explicit PCA-vs-CMA-ES comparison table, and
the CMA-ME reuse pattern (same update math, redefined "success"
signal to include cell-filling and elite-improvement). Cross-linked
from [concepts.md](concepts/concepts.md),
[index.md](index.md),
[evolutionary-computation.md](foundations/evolutionary-computation.md),
and
[novelty-and-quality-diversity.md](concepts/novelty-and-quality-diversity.md).

## [2026-04-19] lint | Clarity pass — acronym expansion + jargon glosses

Systematic pass for reader clarity. Added first-occurrence acronym
expansions and one-line glosses for the most-repeated previously
unexplained terms across the wiki:

- Paired Open-Ended Trailblazer (POET) expanded in `coevolution.md`,
  `automatic-curriculum.md`, `open-ended-evolution.md`,
  `evolutionary-computation.md`, `mcc.md`, `paired.md`,
  `unsupervised-environment-design.md`.
- Adversarially Compounding Complexity by Editing Levels (ACCEL)
  expanded in `unsupervised-environment-design.md`,
  `automatic-curriculum.md`, `coevolution.md`,
  `evolutionary-computation.md`, `paired.md`.
- Prioritized Level Replay (PLR) expanded consistently across
  `paired.md`, `regret-as-objective.md`,
  `unsupervised-environment-design.md`,
  `automatic-curriculum.md`.
- Added a one-line gloss for Strassen's algorithm in `alphaevolve.md`
  and `llm-driven-evolution.md`.
- Added a one-line gloss for Royal Road functions in
  `island-ga.md`.
- Expanded Chromaria from bare name to a full gloss in
  `open-ended-evolution.md` and `minimal-criterion.md`.
- Expanded FunSearch with domain context (cap sets, Nature venue) in
  `alphaevolve.md`, `shinkaevolve.md`, `llm-driven-evolution.md`.

All 385 internal links verified to resolve.

## [2026-04-19] sweep | Acronym first-use expansion + CLAUDE.md rule

Applied a style sweep across all 22 wiki pages to expand acronyms on
first use with the short form in parentheses — e.g. "Quality Diversity
(QD)", "Covariance Matrix Adaptation Evolution Strategy (CMA-ES)",
"NeuroEvolution of Augmenting Topologies (NEAT)", "Minimal Criterion
(MC)", "Behavior Characterization (BC)", "Reinforcement Learning (RL)",
"Large Language Model (LLM)", "Unsupervised Environment Design (UED)",
"Proximal Policy Optimization (PPO)", "Underspecified Partially
Observable Markov Decision Process (UPOMDP)", "Prioritized Level
Replay (PLR)", "Domain Randomization (DR)", "Distributed Genetic
Algorithm (DGA)", "Canonical serial Genetic Algorithm (CGA)", "Tensor
Processing Unit (TPU)", "State-Of-The-Art (SOTA)", "Mixture-of-Experts
(MoE)", "American Invitational Mathematics Examination (AIME)", and
several more. Added the corresponding rule under "Style rules" in
CLAUDE.md and a matching lint check under the lint workflow. All 375
internal links re-verified to resolve.

## [2026-04-19] ingest | foundation papers (9 PDFs, no leaves yet)

Downloaded nine PDFs into `papers/foundations/` as supporting
references for the existing leaves and for future ingests:

- `neat_stanley-miikkulainen_2002.pdf` — NEAT, cited by MCC.
- `novelty-search_lehman-stanley_2011.pdf` — "Abandoning Objectives",
  cited by MCC and ShinkaEvolve.
- `map-elites_mouret-clune_1504.04909.pdf` — MAP-Elites, the canonical
  QD method.
- `funsearch_romera-paredes_2024.pdf` — direct predecessor of
  AlphaEvolve and ShinkaEvolve.
- `poet_wang_1901.01753.pdf` — population-based coevolutionary
  predecessor of PAIRED.
- `plr_jiang_2010.03934.pdf` — Prioritized Level Replay; often
  supersedes PAIRED in practice.
- `cma-es-tutorial_hansen_1604.00772.pdf` — Hansen's CMA-ES tutorial,
  the canonical modern EC reference.
- `cma-me_fontaine_1912.02400.pdf` — CMA-ME, CMA-ES inside MAP-Elites.
- `l-shade_tanabe-fukunaga_2014.pdf` — L-SHADE, the dominant
  Differential Evolution variant on continuous benchmarks.

These live alongside the existing leaves' PDFs but do not yet have
dedicated wiki pages. Future ingests should promote them to leaves
under the standard paper-leaf template.

## [2026-04-19] ingest | ShinkaEvolve (arXiv:2509.19349)

Added Lange, Imajuku, Cetin, "ShinkaEvolve: Towards Open-Ended and
Sample-Efficient Program Evolution", Sakana AI 2025. Created
[papers/shinkaevolve.md](papers/shinkaevolve.md). Touched
[llm-driven-evolution.md](concepts/llm-driven-evolution.md) (added as
canonical example alongside AlphaEvolve),
[parallel-and-distributed-ga.md](concepts/parallel-and-distributed-ga.md)
(ShinkaEvolve cites Tanese 1989 directly),
[novelty-and-quality-diversity.md](concepts/novelty-and-quality-diversity.md)
(ShinkaEvolve's code-novelty rejection sampling is novelty search
re-implemented on top of code embeddings), and
[open-ended-evolution.md](concepts/open-ended-evolution.md)
(explicit framing in the title).
