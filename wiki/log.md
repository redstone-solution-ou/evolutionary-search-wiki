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

## [2026-04-19] promote | Prioritized Level Replay (Jiang et al., ICML 2021)

Promoted Jiang, Grefenstette & Rocktäschel, *"Prioritized Level
Replay"* (arXiv:2010.03934, ICML 2021) from foundation-PDF
reference to a full paper leaf at
[papers/plr.md](papers/plr.md). Moved the PDF from
`papers/foundations/` to `papers/` accordingly. Added to
[papers/papers.md](papers/papers.md) under the "deep-
reinforcement-learning adversarial environment design" lineage
alongside PAIRED. Replaced all previous inline mentions of
Prioritized Level Replay in the concept pages
([paired.md](papers/paired.md),
[unsupervised-environment-design.md](concepts/unsupervised-environment-design.md),
[regret-as-objective.md](concepts/regret-as-objective.md),
[automatic-curriculum.md](concepts/automatic-curriculum.md)) with
links to the new leaf. Updated the slug list in
[CLAUDE.md](../CLAUDE.md) (ten slugs now) and the overview paper
map.

Rationale for the promotion: a confidential research conversation
independently rederived a Prioritized-Level-Replay-adjacent design
(budget-only antagonist producing a learning-potential signal,
no learned environment adversary). The prior foundation-PDF
reference was insufficient — this technique deserves first-class
treatment as the dominant alternative to Protagonist Antagonist
Induced Regret Environment Design.

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

## [2026-04-24] lint + restructure | Narrative entry pass

Combined lint pass and a readability-driven restructure of the entry
experience. Lint side: re-verified all 538 internal links resolve,
all 10 paper leaves and 10 concept pages are template-compliant,
zero orphans, zero pages with weak (≤1) inbound connectivity.
Foundation page `evolutionary-computation.md` does not strictly
follow the concept-page template (no `Mechanics` / `Why it works` /
`Trade-offs and failure modes` sections) but reads well as a
narrative umbrella page; left intact rather than forced into the
template.

Restructure side: the user feedback was that landing on the wiki
felt like reading an index rather than learning. Rewrote the four
entry-experience pages so a reader can walk top-to-bottom without
following a single link and come away with the shape of the field.

- [overview.md](overview.md) — rewritten as a single linear
  narrative organised around the four "modifications of the
  canonical loop" (distribute the population; drop the fixed
  objective; couple two populations; replace the variation
  operator). Each lineage is one section that names the canonical
  paper and the concept inline. Reading paths and knowledge-graph
  sketch retained, but moved to the end as reference rather than
  presented as the structure of the page.
- [foundations/foundations.md](foundations/foundations.md) —
  rewritten as a chapter introduction explaining why the canonical
  loop matters as the substrate every later page modifies, with the
  page list moved below the narrative.
- [concepts/concepts.md](concepts/concepts.md) — rewritten as a
  walk through the four families of design choices (what gets
  selected, how populations are coupled, how variation is performed,
  how the population is structured), with each concept page
  motivated by which family it belongs to.
- [papers/papers.md](papers/papers.md) — rewritten so each lineage
  group leads with a paragraph explaining what the lineage *adds*
  rather than just listing leaves; the alphabetical index table
  moved to the end as a quick-lookup reference.

The leaf pages and individual concept pages were not modified —
they are already narrative-quality on their own and serve as
reference cards once a reader has the entry-level framing.

## [2026-04-28] schema | leaf-as-mirror rule + design-rationale lint check (propagated from ts-forecasting-wiki)

Adopted the same schema rule that was introduced in
ts-forecasting-wiki today. Triggered by a request to keep the three
sibling wikis (this one, ts-forecasting-wiki, kg-rag-wiki) in sync on
the leaf-as-mirror principle.

Schema changes in [`CLAUDE.md`](../CLAUDE.md):

- **Paper-leaf preamble** — added the principle: "A leaf is a
  faithful technical mirror of the paper's substantive content."
  Bumped target length 600–1000 → 600–1200 to leave room for design
  rationale on papers that motivate it heavily.
- **`## Method at a glance` template** — rewritten from
  "2-4 sentences: algorithm family, populations, training signal,
  central mechanism" to "one short paragraph that includes the
  rationale behind each non-trivial design choice the paper itself
  motivates," with EC-specific examples (selection operator, mutation
  rate, coevolutionary partner, curriculum, archive structure, regret
  estimator, LLM-as-mutator prompt) and a recommendation to promote a
  single load-bearing rationale to its own H3 subsection (e.g.
  `### How <method> prevents premature convergence`).
- **Anti-patterns** — added: "Do not write a paper leaf that omits
  a design rationale the paper itself motivates," with the same
  discoverable test as ts-forecasting-wiki.
- **Lint workflow** — added "Design-rationale gaps in leaves" as a
  new checklist item.
- **`llm-wiki.md` editing rule** — relaxed from blanket "do not
  edit" to "do not edit as part of routine work," allowing
  structural clarifications to the generic pattern *with explicit
  user direction* and a `schema` op log entry. This brings the
  guard rail in line with ts-forecasting-wiki and kg-rag-wiki.

Generic-pattern propagation in [`llm-wiki.md`](../llm-wiki.md):

- Added a new "leaf is a faithful mirror" convention in
  §Architecture, just before §Operations. This is the generic
  principle, applicable to any LLM-built wiki where leaves describe
  substantive sources; the same paragraph was added simultaneously
  to ts-forecasting-wiki and kg-rag-wiki.

No paper leaves were lint-fixed in this pass — the rule is
prospective for new ingests and existing leaves will be checked in a
follow-up lint pass as the corpus grows past the current 10 leaves.

Scope notes:

- This wiki currently has 10 paper leaves (`alphaevolve`,
  `alphazero`, `grammatical-evolution`, `island-ga`, `lmx`, `mcc`,
  `model-merging`, `paired`, `plr`, `shinkaevolve`). Spot-check
  candidates for rationale gaps when a future lint pass runs:
  `paired` (regret-as-objective), `mcc` (minimal-criterion
  rationale), `shinkaevolve` (LLM-as-mutator prompt + selection
  rationale), `plr` (level-replay scoring rationale), `island-ga`
  (migration topology + interval rationale).
- The change is purely schematic; no behavioural change to the
  existing wiki content.

## [2026-04-28] ingest | EPPO (Videau, Leite, Schoenauer, Teytaud, arXiv:2412.04291)

Added Mathurin Videau, Alessandro Leite, Marc Schoenauer & Olivier
Teytaud, *"Evolutionary Pre-Prompt Optimization for Mathematical
Reasoning"*, 2024 preprint / 2026 v2. EPPO frames task-level few-shot
Chain-of-Thought prompt design as a discrete combinatorial
optimization problem: encode a pre-prompt as a length-s integer array
indexing a curated demonstration pool, and search the index space with
a Nevergrad comparison-based EA (Discrete (1+1)-ES, Portfolio,
DiscLengler, …). The paper proves an information-theoretic
generalization-risk bound `Pr(|L̂(r) − L(r)| > ε) ≤ κ^b · δ_{1,ε}`
that depends only on optimizer feedback arity κ and budget b, and
demonstrates LLaMA2-70B exact-match gains exceeding 10 absolute points
on GSM8k and MathQA over standard CoT, additive with self-consistency.

Created [papers/eppo.md](papers/eppo.md) following the leaf-as-mirror
schema introduced earlier today; the load-bearing
comparison-based-feedback rationale is promoted to its own H3
subsection (`### How comparison-based feedback bounds the
generalization risk`) inside *Method at a glance*. Opened a new
lineage section *"Evolutionary search over LLM prompts"* in
[papers/papers.md](papers/papers.md), positioned between
*LLM-driven program evolution* and *Evolutionary merging of trained
weights*; added EPPO to the alphabetical index. Cross-linked the
inverted-pattern relationship (LLM as fitness oracle vs LLM as
mutator) into [concepts/llm-driven-evolution.md](concepts/llm-driven-evolution.md)
under *Design choices in the literature*. Updated
[index.md](index.md) and the canonical-slug list in
[../CLAUDE.md](../CLAUDE.md).

Eleven paper leaves now (`alphaevolve`, `alphazero`, `eppo`,
`grammatical-evolution`, `island-ga`, `lmx`, `mcc`, `model-merging`,
`paired`, `plr`, `shinkaevolve`).

Concept-page deferral: a dedicated `evolutionary-prompt-optimization`
concept page is a candidate once a second paper in this lineage is
ingested (Promptbreeder, Fernando et al. ICML 2024, is the obvious
companion; Videau et al.'s own *Evolutionary Retrofitting*
(arXiv:2410.11330) is another). For now the inverted-pattern note in
`llm-driven-evolution.md` carries the cross-cutting framing.

## [2026-04-28] ingest | Promptbreeder (Fernando, Banarse, Michalewski, Osindero, Rocktäschel, arXiv:2309.16797)

Added Chrisantha Fernando, Dylan Banarse, Henryk Michalewski, Simon
Osindero & Tim Rocktäschel, *"Promptbreeder: Self-Referential
Self-Improvement via Prompt Evolution"*, Google DeepMind 2023
preprint / ICML 2024. Promptbreeder evolves (task-prompt,
mutation-prompt) pairs with PaLM 2-L acting as the variation operator
on both — task-prompts are mutated by `LLM(M + P)` and mutation-prompts
themselves are mutated by `LLM(H + M)` in a hyper-mutation step that
makes the system self-referential. Nine concrete mutation operators in
five classes (direct, EDA, hyper-mutation, Lamarckian,
crossover/context-shuffling) drive a binary-tournament GA over a
population of 50 for 20–30 generations. Reaches 83.9% zero-shot on
GSM8K with the evolved task-prompt `"SOLUTION:"`, surpassing OPRO and
Plan-and-Solve on the same underlying LLM; 89% on ETHOS hate-speech
classification.

Created [papers/promptbreeder.md](papers/promptbreeder.md) following
the leaf-as-mirror schema; the load-bearing self-reference machinery
is promoted to its own H3 (`### How self-reference is implemented`)
inside *Method at a glance*, plus a second H3 on the diversity
machinery that motivates the BERT-similarity rejection threshold.

Slotted into the existing *"Evolutionary search over LLM prompts"*
lineage in [papers/papers.md](papers/papers.md) (rewritten
introduction paragraph to acknowledge the two complementary
subpatterns: classical-EA + LLM-as-fitness-oracle vs LLM-as-mutator);
added to the alphabetical index. Cross-linked into
[concepts/llm-driven-evolution.md](concepts/llm-driven-evolution.md)
as the most explicit instantiation of "LLM as variation operator"
outside the program-evolution setting. Updated [index.md](index.md)
and the canonical-slug list in [../CLAUDE.md](../CLAUDE.md).

Twelve paper leaves now (`alphaevolve`, `alphazero`, `eppo`,
`grammatical-evolution`, `island-ga`, `lmx`, `mcc`, `model-merging`,
`paired`, `plr`, `promptbreeder`, `shinkaevolve`).

EvoPrompt (Guo et al., ICLR 2024, arXiv:2309.08532) is referenced by
name in the Promptbreeder leaf's *Follow-up work* section as the
concurrent ICLR 2024 GA/DE-flavoured cousin; ingest scheduled as the
next branch in this autonomous run, with the
`evolutionary-prompt-optimization` concept page to follow once both
papers and EPPO are leaves.

## [2026-04-29] ingest | EvoPrompt (Guo, Wang, Guo, Li, Song, Tan, Liu, Bian, Yang, arXiv:2309.08532)

Added Qingyan Guo et al., *"EvoPrompt: Connecting LLMs with
Evolutionary Algorithms Yields Powerful Prompt Optimizers"*, ICLR
2024. EvoPrompt connects LLMs to classical EAs by instructing the LLM
in natural language to perform mutation and crossover on candidate
prompts, while keeping the EA scaffold (selection, fitness on a dev
set, population update) classical. Two instantiations: **EvoPrompt
(GA)** with roulette-wheel selection and a two-step
crossover-then-mutate template, and **EvoPrompt (DE)** with a
four-step Differential-Evolution template that identifies different
parts between two parents, mutates only those, combines with the
current best, and crosses over with the base prompt. Evaluated on 31
datasets across language understanding (7), generation (2), and
Big-Bench Hard (22), on both Alpaca-7B and GPT-3.5. Differential
Evolution wins on harder generation and Big-Bench Hard tasks (up to
25% improvement on individual BBH tasks; 3.5% average) and on
low-quality initial populations; Genetic Algorithm edges out on
simpler classification. Open source at
[github.com/beeevita/EvoPrompt](https://github.com/beeevita/EvoPrompt).

Created [papers/evoprompt.md](papers/evoprompt.md) following the
leaf-as-mirror schema; two H3 subsections in *Method at a glance*
carry the load-bearing rationale (`### Why mutation-on-different-parts
is the load-bearing DE choice` and `### Why the selection strategy
and population quality are reported`).

Slotted into the existing *"Evolutionary search over LLM prompts"*
lineage in [papers/papers.md](papers/papers.md) (now three leaves:
EPPO, Promptbreeder, EvoPrompt); added to the alphabetical index.
Cross-linked into
[concepts/llm-driven-evolution.md](concepts/llm-driven-evolution.md)
under *Design choices in the literature*. Updated
[index.md](index.md) and the canonical-slug list in
[../CLAUDE.md](../CLAUDE.md).

Thirteen paper leaves now (`alphaevolve`, `alphazero`, `eppo`,
`evoprompt`, `grammatical-evolution`, `island-ga`, `lmx`, `mcc`,
`model-merging`, `paired`, `plr`, `promptbreeder`, `shinkaevolve`).

The `evolutionary-prompt-optimization` concept page (covering EPPO,
Promptbreeder, EvoPrompt as the three exemplars) is the next
scheduled branch in this autonomous run — the lineage now has the
critical mass of three papers required to motivate a dedicated
concept page rather than relying on the inverted-pattern note in
`llm-driven-evolution.md`.

## [2026-04-29] concept | evolutionary-prompt-optimization

Created [concepts/evolutionary-prompt-optimization.md](concepts/evolutionary-prompt-optimization.md)
as the cross-cutting concept page for evolutionary search over the
prompt handed to a fixed LLM at inference time. The page motivates the
two complementary subpatterns now visible in the wiki — classical-EA +
LLM-as-fitness-oracle ([EPPO](papers/eppo.md)) and LLM-as-mutator
([Promptbreeder](papers/promptbreeder.md),
[EvoPrompt](papers/evoprompt.md)) — and presents them with shared
mechanics, a side-by-side trade-off table, and a unified set of
open questions. Cross-references three exemplar leaves under *Papers
that exemplify this*.

Wired into the existing knowledge graph:

- Added a new paragraph to the *"How variation is performed"* family
  in [concepts/concepts.md](concepts/concepts.md) introducing the
  prompt-evolution thread alongside the existing
  [llm-driven-evolution](concepts/llm-driven-evolution.md) paragraph;
  added an entry to the *Pages in this section* alphabetical list.
- Added the new concept to the *Related concepts* line in each of the
  three exemplar leaves' *In the knowledge graph* blocks
  ([eppo](papers/eppo.md), [promptbreeder](papers/promptbreeder.md),
  [evoprompt](papers/evoprompt.md)).
- Added a back-link from
  [concepts/llm-driven-evolution.md](concepts/llm-driven-evolution.md)
  in its *Related wiki pages* section, framing the relationship as
  "the prompt-flavoured sibling concept; the LLM-as-mutator subpattern
  there overlaps with the program-evolution work catalogued on this
  page."
- Updated [index.md](index.md) with a new *Concepts* entry.

The concept page satisfies the schema's cross-link minimum — three
papers exemplifying it (EPPO, Promptbreeder, EvoPrompt), two concept
pages cross-linked (`llm-driven-evolution`,
`novelty-and-quality-diversity`, `parallel-and-distributed-ga`) and
two foundation pages (`evolutionary-computation`,
`genetic-algorithms`).

This closes out the autonomous run that began with the EPPO ingest:
in order, four PRs landed on `main` — EPPO leaf (PR #3), Promptbreeder
leaf (PR #4), EvoPrompt leaf (PR #5), and this concept page. Each on
its own short-lived feature branch per the CLAUDE.md "one focused
change per branch" rule.
