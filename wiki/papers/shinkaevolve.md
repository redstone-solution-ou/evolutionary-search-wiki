# ShinkaEvolve: Towards Open-Ended and Sample-Efficient Program Evolution

> **Short name:** `shinkaevolve` · **arXiv:** [2509.19349](https://arxiv.org/abs/2509.19349) · **PDF:** [local](../../papers/shinkaevolve_2509.19349.pdf) · **Date:** 2025-09 · **Venue:** Technical report (Sakana AI)

**Authors:** Robert Tjarko Lange, Yuki Imajuku, Edoardo Cetin (Sakana AI)

## Abstract
ShinkaEvolve is an open-source LLM-driven program-evolution framework
designed for sample efficiency. Existing approaches (FunSearch,
AlphaEvolve, OpenEvolve) require thousands of LLM evaluations to find
useful solutions; ShinkaEvolve introduces three innovations that
substantially reduce this cost: (1) an adaptive parent-and-LLM
sampling scheme that balances exploration and exploitation via
power-law and weighted sampling, (2) code novelty rejection-sampling
based on embedding similarity plus an LLM-as-novelty-judge, and
(3) a bandit-based LLM ensemble selection strategy that adapts to the
evolving archive. ShinkaEvolve discovers a state-of-the-art circle
packing in 150 samples, designs high-performing agentic harnesses for
AIME mathematical reasoning, identifies improvements to ALE-Bench
competitive programming solutions, and discovers novel mixture-of-
experts load-balancing loss functions. Released under Apache 2.0.

## Key contributions
- Three algorithmic innovations stacked on top of the
  [LLM-driven evolution](../concepts/llm-driven-evolution.md) recipe:
  power-law / weighted parent sampling, code-embedding novelty
  rejection sampling, and bandit-based LLM ensemble selection.
- Empirical demonstration of an order-of-magnitude reduction in the
  number of LLM samples needed to reach state-of-the-art on multiple
  domains: 150 samples on circle packing, similarly low budgets on
  AIME agentic-harness design, ALE-Bench competitive programming, and
  MoE load-balancing loss design.
- Fully open-source release (Apache 2.0) of the framework, including
  an interactive visualization tool for monitoring search progress —
  the first open counterpart to [AlphaEvolve](./alphaevolve.md).
- Detailed Section 3.1 treatment of parent-sampling strategies as a
  family parameterized by an exploitation knob α (uniform, power-law,
  hill-climbing) plus a separate weighted scheme combining sigmoid-
  scaled performance and inverse-offspring-count novelty.
- Reuse of the [island model](../concepts/parallel-and-distributed-ga.md)
  inherited from FunSearch and AlphaEvolve, with explicit citation of
  Tanese 1989 — making the lineage from classical distributed GAs to
  modern LLM-evolution explicit in the bibliography.

## Method at a glance
The control flow has three phases per iteration. (1) *Parent and
inspiration sampling*: pick an island, pick a parent with one of
several strategies (power-law over fitness rank, or weighted by
performance and novelty), pick top-K and random inspiration programs.
(2) *Program mutation*: sample an LLM from the ensemble (bandit
weights), generate a code change as a SEARCH/REPLACE diff, full
rewrite, or crossover with another archive program; reject if a code-
embedding cosine-similarity check plus an LLM novelty judge says the
candidate is too similar to existing archive members. (3) *Execution
and feedback*: run the candidate, collect a scalar fitness plus public
metrics and textual feedback, register in the archive, update bandit
weights for LLM selection, update an online "meta-scratchpad" of
distilled lessons.

## Why it matters
ShinkaEvolve is the first open-source LLM-evolution framework to
reach AlphaEvolve-class results, and it does so at one to two orders
of magnitude lower sample cost. This matters operationally — small
labs and individuals can now run program-evolution experiments that
previously required Google-scale infrastructure — and methodologically,
because it makes the "which component does the work" question
answerable through ablation. Its three innovations (sampling, novelty
rejection, bandit ensemble) factor cleanly and can each be studied or
swapped independently.

## Strengths
- Sample efficiency on a verifiable benchmark (circle packing): the
  150-sample SOTA result is a strong, falsifiable claim that the new
  components do meaningful work, not just renaming of existing pieces.
- Genuine open-source release including code, prompts, and a
  visualization tool. This is a substantive shift from the
  AlphaEvolve white-paper-only release pattern.
- Clean factorization of algorithmic innovations: sampling strategy,
  novelty filter, and ensemble selection are independent and each can
  be ablated. The paper does not push hard on per-component ablations
  but the architecture supports them.
- Wide empirical coverage: four very different domains (geometric
  optimization, agentic reasoning harness, competitive programming,
  ML loss design) — argues the method is not a one-domain trick.
- Direct citation of Tanese 1989 for the island model is the rare
  modern paper that ties the new LLM-evolution thread back to the
  1980s parallel-GA literature.

## Limitations and open critiques
- Sample efficiency is reported per *LLM call*, but the LLMs in the
  ensemble (GPT, Gemini, Claude, DeepSeek) have very different
  per-call costs. A per-dollar comparison would be more meaningful;
  the paper does not provide one.
- Ablations are reported but not exhaustive. The paper does not
  cleanly separate the contribution of each of the three innovations,
  so it is hard to tell whether sample efficiency comes mostly from
  novelty rejection, mostly from bandit ensemble selection, or from
  the combination.
- The "code novelty" cosine-similarity threshold (η = 0.95 in the
  paper's example) is a hyperparameter without a principled choice
  procedure. Domain-dependent tuning is implied but not characterized.
- Comparison to AlphaEvolve is necessarily indirect — AlphaEvolve is
  closed-source and the only common benchmark is circle packing. The
  paper is honest about this; the reader has to be too.
- The "open-ended" claim in the title is aspirational. The paper
  demonstrates better sample efficiency but does not show the
  algorithm *running indefinitely* and continuing to produce
  qualitatively new results — the [open-ended evolution](../concepts/open-ended-evolution.md)
  bar.

## Follow-up work and dialogue
ShinkaEvolve is the most recent paper in this wiki and the dominant
open-source baseline as of April 2026 for follow-up work in
LLM-driven program evolution. Conceptually it sits between
[AlphaEvolve](./alphaevolve.md) (the closed-source, scaled-up
predecessor) and the broader open-ended-search agenda — citing
Stanley et al. 2017 and Lehman & Stanley work alongside Romera-Paredes
et al. 2024 makes the lineage from
[novelty / quality diversity](../concepts/novelty-and-quality-diversity.md)
and [open-ended evolution](../concepts/open-ended-evolution.md)
explicit. The Sakana AI line continues with related work on AI
Scientist and Darwin Gödel Machine; whether the three ShinkaEvolve
innovations transfer to those settings is open.

## Reproducibility
- **Open code:** yes, Apache 2.0, at
  `github.com/SakanaAI/ShinkaEvolve` (cited in the paper). Includes
  implementation details and an interactive visualization tool.
- **Domains used:** circle packing, AIME mathematical-reasoning
  agentic harness design, ALE-Bench competitive programming,
  mixture-of-experts load-balancing loss design. Four very different
  evaluator types (geometric, agentic, code-execution, ML training).
- **Compute disclosed:** sample counts are reported per experiment
  (e.g., 150 for circle packing); per-call wall-clock and dollar cost
  depend on the chosen LLM and are not aggregated.
- **Hyperparameters:** parent-sampling power-law exponent α and
  weighted-sampling sigmoid temperature λ are tunable; novelty
  threshold η = 0.95 is the example value. The bandit-based ensemble
  selection adapts online with no human-tuned weights.

## When to cite this paper
Cite ShinkaEvolve as the canonical reference for *sample-efficient,
open-source* LLM-driven program evolution — and as the best current
target for community ablations of the LLM-evolution pattern. The three
innovations (power-law / weighted parent sampling, code-embedding
novelty rejection, bandit-based LLM ensemble) should each be cited to
this paper individually. For the *scaled, closed* counterpart, cite
[AlphaEvolve](./alphaevolve.md). For the original LLM-evolution
pattern, cite FunSearch.

## In the knowledge graph
- **Related concepts:** [LLM-driven evolution](../concepts/llm-driven-evolution.md),
  [parallel and distributed GA](../concepts/parallel-and-distributed-ga.md)
  (island populations with citation of Tanese 1989),
  [novelty and quality diversity](../concepts/novelty-and-quality-diversity.md)
  (rejection sampling reuses the novelty-pressure idea on top of code
  embeddings), [open-ended evolution](../concepts/open-ended-evolution.md)
  (explicit framing in the title)
- **Foundations:** [genetic algorithms](../foundations/genetic-algorithms.md),
  [evolutionary computation](../foundations/evolutionary-computation.md)
- **See also:** [AlphaEvolve](./alphaevolve.md) (the closed-source,
  scaled-up predecessor), [Island GA](./island-ga.md) (the population-
  structure ancestor)
