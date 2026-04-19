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
