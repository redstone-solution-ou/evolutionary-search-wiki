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

## [2026-04-19] ingest | Grammatical Evolution (O'Neill & Ryan, IEEE TEC 2001)

Added O'Neill & Ryan, *"Grammatical Evolution"*, IEEE Transactions
on Evolutionary Computation 5(4):349-358, 2001. Created
[papers/grammatical-evolution.md](papers/grammatical-evolution.md).
The canonical non-LLM framework for evolutionary search over
structured outputs constrained by a formal grammar — including
JSON-schema-constrained configurations, DSL programs, and any
other grammar-definable structured representation. Genotype =
variable-length integer string; phenotype = grammar-decoded
structured output; genetic operators act on the genotype; grammar
guarantees syntactic validity of every offspring.

Added new lineage grouping "Grammar- / schema-constrained
evolutionary search" to [papers/papers.md](papers/papers.md) with
this paper as its current sole leaf. Cross-linked from
[llm-driven-evolution.md](concepts/llm-driven-evolution.md) as the
non-LLM counterpart to [LMX](papers/lmx.md) — both aim at syntactic
validity of structured offspring, GE via hand-written grammar, LMX
via learned LLM distribution; complementary rather than competing.
Updated slug list in [CLAUDE.md](../CLAUDE.md) (nine slugs now).

## [2026-04-19] ingest | LMX — Language Model Crossover (arXiv:2302.12170)

Added Meyerson, Nelson, Bradley, Gaier, Moradi, Hoover & Lehman,
*"Language Model Crossover: Variation through Few-Shot Prompting"*,
ACM Transactions on Evolutionary Learning and Optimization 2024
(arXiv preprint 2023). Created [papers/lmx.md](papers/lmx.md).
This is the canonical "LLM as general-purpose crossover operator
over arbitrary text-serialisable genotypes" paper — the theoretical
foundation for AlphaEvolve / ShinkaEvolve / FunSearch as well as
for any future work evolving JSON configs, YAML pipelines, DSL
programs, or other structured-text representations.

Positioned in [concepts/llm-driven-evolution.md](concepts/llm-driven-evolution.md)
"Design choices in the literature" as the crossover-side companion
to Evolution through Large Models (ELM; Lehman et al. 2022, the
mutation-side predecessor, which is not separately ingested but is
referenced in the LMX leaf and concept page). Added to
[papers/papers.md](papers/papers.md) under the "LLM-driven program
evolution" lineage grouping, reordered so LMX appears first (as
the foundational paper) followed by AlphaEvolve and ShinkaEvolve
(as the scaled application systems). Updated the slug list in
[CLAUDE.md](../CLAUDE.md) (eight slugs now) and the overview
paper map.

## [2026-04-19] ingest | Model Merging (Akiba et al., arXiv:2403.13187)

Added Akiba, Shing, Tang, Sun & Ha, *"Evolutionary Optimization of
Model Merging Recipes"*, Sakana AI 2024 arXiv preprint (later
published in Nature Machine Intelligence). Created
[papers/model-merging.md](papers/model-merging.md). Added new
lineage grouping "Evolutionary merging of trained weights" to
[papers/papers.md](papers/papers.md) with this paper as the sole
current leaf. Cross-linked from
[llm-driven-evolution.md](concepts/llm-driven-evolution.md)
(positioned as the "evolve weights" counterpart to the "evolve
code" AlphaEvolve / ShinkaEvolve thread) and from
[cma-es.md](concepts/cma-es.md) (the paper uses CMA-ES for its
continuous parameter-space merging-coefficient search, a clean
application of the algorithm). Updated the slug list in
[CLAUDE.md](../CLAUDE.md) and the overview paper map.

## [2026-04-19] ingest | AlphaZero (arXiv:1712.01815)

Added Silver et al., *"Mastering Chess and Shogi by Self-Play with a
General Reinforcement Learning Algorithm"*, DeepMind arXiv preprint
2017, published in Science 2018 as *"A general reinforcement
learning algorithm that masters chess, shogi, and Go through
self-play"*. Created [papers/alphazero.md](papers/alphazero.md).
Added the new "Self-play coevolution" lineage to
[papers/papers.md](papers/papers.md) with AlphaZero as its sole
current leaf. Cross-linked from
[coevolution.md](concepts/coevolution.md) ("Papers that exemplify
this" + the symmetric-self-play entry in "Design choices in the
literature") and from
[automatic-curriculum.md](concepts/automatic-curriculum.md) (added a
new "Symmetric self-play" mechanism section alongside Adversarial /
Selection-from-pool / Coevolutionary viability). Updated the slug
list in [CLAUDE.md](../CLAUDE.md) and the scope paragraph in
[../README.md](../README.md).

## [2026-04-19] sweep | Full-title expansion for foundation-PDF citations

Applied a pass to ensure that every citation of a foundation PDF in
the wiki (those without a dedicated leaf) spells out the paper's
full title in adjacent prose, per feedback that filenames alone
(`poet_wang_1901.01753.pdf` etc.) are opaque to readers who do not
already know the paper. Touched
[concepts/cma-es.md](concepts/cma-es.md) ("Papers that exemplify
this" block) and the foundation-paper block of this log, adding
full titles, authors, and venues alongside the filenames.

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

- *"Evolving Neural Networks through Augmenting Topologies"* —
  Stanley & Miikkulainen, Evolutionary Computation 10(2), 2002.
  `neat_stanley-miikkulainen_2002.pdf`. Cited by MCC.
- *"Abandoning Objectives: Evolution Through the Search for Novelty
  Alone"* — Lehman & Stanley, Evolutionary Computation 19(2), 2011.
  `novelty-search_lehman-stanley_2011.pdf`. Cited by MCC and
  ShinkaEvolve.
- *"Illuminating search spaces by mapping elites"* — Mouret & Clune,
  arXiv:1504.04909, 2015. `map-elites_mouret-clune_1504.04909.pdf`.
  The canonical QD method.
- *"Mathematical discoveries from program search with large language
  models"* — Romera-Paredes et al., Nature 2024.
  `funsearch_romera-paredes_2024.pdf`. Direct predecessor of
  AlphaEvolve and ShinkaEvolve.
- *"Paired Open-Ended Trailblazer (POET): Endlessly Generating
  Increasingly Complex and Diverse Learning Environments and Their
  Solutions"* — Wang, Lehman, Clune & Stanley, ICML 2019,
  arXiv:1901.01753. `poet_wang_1901.01753.pdf`. Population-based
  coevolutionary predecessor of PAIRED.
- *"Prioritized Level Replay"* — Jiang, Grefenstette & Rocktäschel,
  ICML 2021, arXiv:2010.03934. `plr_jiang_2010.03934.pdf`. Often
  supersedes PAIRED in practice.
- *"The CMA Evolution Strategy: A Tutorial"* — Hansen, arXiv:1604.00772,
  2016. `cma-es-tutorial_hansen_1604.00772.pdf`. The canonical modern
  EC reference.
- *"Covariance Matrix Adaptation for the Rapid Illumination of
  Behavior Space"* — Fontaine, Togelius, Nikolaidis & Hoover, GECCO
  2020, arXiv:1912.02400. `cma-me_fontaine_1912.02400.pdf`. CMA-ES
  inside MAP-Elites.
- *"Improving the Search Performance of SHADE using Linear Population
  Size Reduction"* — Tanabe & Fukunaga, IEEE CEC 2014.
  `l-shade_tanabe-fukunaga_2014.pdf`. The dominant Differential
  Evolution variant on continuous benchmarks.

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
