# Evolutionary Optimization of Model Merging Recipes

> **Short name:** `model-merging` · **arXiv:** [2403.13187](https://arxiv.org/abs/2403.13187) · **PDF:** [local](../../papers/model-merging_akiba_2403.13187.pdf) · **Date:** 2024-03 · **Venue:** preprint, later published in Nature Machine Intelligence

**Authors:** Takuya Akiba, Makoto Shing, Yujin Tang, Qi Sun, David Ha (Sakana AI)

## Abstract
Model merging — combining the weights of several pretrained Large
Language Models (LLMs) into a single composite model — has become a
dominant strategy on open leaderboards, but the recipes for merging
(which layers to combine, in what proportions, in what order) are
typically hand-designed by practitioners with deep domain knowledge.
Akiba et al. propose an **evolutionary** approach that automatically
searches over merging recipes in two complementary spaces: the
continuous **parameter space** of weight-interpolation coefficients,
and the combinatorial **data flow space** of which layers from which
source model appear in what order in the final network. The combined
search discovers non-obvious merges that outperform human heuristics;
a Japanese-language LLM merged with an English mathematical-reasoning
LLM produces a Japanese Math LLM that beats much larger specialised
models on Japanese math benchmarks without any additional training.
The technique applies equally to vision-language models and produces
culturally-aware Japanese VLMs.

## Key contributions
- **Model merging framed as an evolutionary search problem**, rather
  than a hand-designed heuristic. The "genome" is the merging recipe;
  fitness is downstream benchmark performance of the merged model.
- **Two complementary search spaces**:
  - *Parameter space.* Continuous interpolation coefficients
    (DARE, TIES, linear, spherical linear) between pretrained
    source models; searched with Covariance Matrix Adaptation
    Evolution Strategy ([CMA-ES](../concepts/cma-es.md)).
  - *Data flow space.* Discrete combinatorial choice of which
    layers from which source model are stacked in what order to
    form the merged network; searched with a custom
    evolutionary operator.
- **Zero-training reproduction operator.** Merging is an arithmetic
  operation on existing weights; no gradient descent is required to
  produce a new candidate. This is roughly a thousand times cheaper
  than full fine-tuning per candidate, which is what makes
  evolutionary search practical here.
- **Cross-domain merging.** Demonstrates that merging two source
  models from entirely different domains (Japanese language + English
  math, Japanese language + English vision) yields composites that
  inherit capabilities from both, without either parent being trained
  on the combined task. This is the "surprising" empirical finding of
  the paper.
- **State-of-the-art merged models released** back to the open-source
  community (EvoLLM-JP, EvoVLM-JP), lowering the compute barrier for
  the community to build specialised models.

## Method at a glance
The search operates in parameter and data-flow spaces either
independently or jointly. For parameter-space search, each candidate
is a vector of merging coefficients (e.g., DARE sparsification
probabilities, TIES trim-elect-disjoint weights); CMA-ES adapts the
covariance of the proposal Gaussian over this vector based on
downstream benchmark scores. For data-flow space, each candidate is
a sequence of (source-model-index, layer-index) pairs specifying the
composite network's layers; evolutionary operators mutate and
recombine these sequences. In the combined setting, the
parameter-space vector and the data-flow sequence are searched
jointly, treating the composite merge recipe as the genome.

## Why it matters
This paper demonstrates the first empirically successful
**evolutionary search over trained neural networks themselves** —
not over their code, not over their training recipes, but over
arithmetic combinations of their parameters. The reproduction
operator is cheap (arithmetic), the evaluation is end-to-end
benchmark-scored, and the search discovers non-obvious recipes that
cross domain boundaries. For practitioners who want an evolutionary
outer loop around pretrained foundation models — but cannot afford
to train each offspring from scratch — this is the template. It
slots naturally into the discussion in the broader
[LLM-driven evolution](../concepts/llm-driven-evolution.md) thread
as the "evolution over weights, not code" branch. Structurally also
related to [AlphaEvolve](./alphaevolve.md) and
[ShinkaEvolve](./shinkaevolve.md), which evolve *code*; Akiba et al.
evolve *parameters* of trained networks.

## Strengths
- **Zero-training reproduction is the right cost structure** for
  evolutionary search over deep networks. Sakana here solves the
  problem that made NEAT-era neuroevolution stall at large
  architectures: you do not need gradients or training per offspring.
- **Parameter space plus data flow space is a genuinely new design
  axis.** Prior merging work searched one or the other; combining
  them allows the algorithm to discover recipes that neither
  sub-search would find alone. The ablation in §4 shows both axes
  contribute.
- **Uses CMA-ES appropriately.** The continuous-merging-coefficient
  search is a textbook continuous black-box optimization problem;
  CMA-ES is the right hammer. Good alignment between algorithm and
  problem structure.
- **Cross-domain merging works.** The Japanese Math LLM result is
  not just a benchmark number — it is a capability the source models
  did not individually possess. This is the strongest form of
  evidence that evolutionary merging does something the source
  models alone could not do.
- **Open-source releases.** The EvoLLM-JP / EvoVLM-JP checkpoints
  and the merging code are released, giving the community a
  reproducible starting point.

## Limitations and open critiques
- **Requires architectural compatibility between source models.**
  Merging assumes the source models share architecture (or at least
  share enough that the parameter-space interpolation has a sensible
  target). The technique does not handle mixing a GRU with a
  Transformer, for example. Heterogeneous-architecture populations
  must be speciated by architecture before merging is applicable.
- **Evaluation is benchmark-dependent.** Fitness is downstream
  benchmark score. Benchmarks are narrow and gameable; the search
  can easily overfit to the specific benchmark suite. The authors
  mitigate by using multi-benchmark fitness averages but do not
  resolve the underlying issue.
- **Compute still nontrivial.** Each evaluation = full benchmark run
  of a merged model, which for LLM-scale models is not cheap. The
  "thousand times cheaper than fine-tuning" comparison is for the
  reproduction step, not the evaluation step; the search may still
  take hundreds of GPU-hours to converge on non-toy problems.
- **Scales to 7B-class LLMs; scaling to frontier-scale (100B+) is
  unproven here.** Whether the merging signal is strong enough at
  larger scales, or whether architecture details at scale (MoE
  routing, parallelism) complicate merging, is an open empirical
  question.
- **Not a training-replacement.** Merging combines *existing*
  capabilities of pretrained models; it does not produce capabilities
  neither parent had. This is a feature, not a bug, but it bounds
  where the technique is applicable: the source-model pool must
  contain the component capabilities one wants to combine.

## Follow-up work and dialogue
This paper is the current reference for *evolutionary search over
trained neural-network weights*. It has spawned a rapidly growing
follow-up literature on automatic merging (MergeKit, Task Vectors
and Arithmetic, DARE, TIES-Merging, etc.), most of which it cites or
co-develops. Conceptually it sits parallel to the
[LLM-driven evolution](../concepts/llm-driven-evolution.md) thread
(AlphaEvolve, ShinkaEvolve): both evolve neural-network-based
systems, but merging operates on trained weights while LLM-driven
code evolution operates on source code. Combining the two — e.g.
using an LLM to propose merging recipes *and* evolving them via
CMA-ES — is an open research direction.

For researchers interested in an evolutionary outer loop over
pretrained networks without gradient-based reproduction, this is the
canonical starting point. It is also the structurally cleanest
demonstration that *the reproduction operator does not have to be
training*.

## Reproducibility
- **Open code:** yes, released by Sakana AI at
  `github.com/SakanaAI/evolutionary-model-merge` (cited in paper).
- **Merged checkpoints:** EvoLLM-JP (7B Japanese Math LLM), EvoVLM-JP
  (Japanese vision-language model) released on HuggingFace.
- **Domains used:** Japanese LLM benchmarks (MGSM-JA, JP-LMEH) for
  the Math merging experiment; Japanese VLM benchmarks (JA-VG-VQA,
  JA-VLM-Bench-In-the-Wild) for the vision experiment.
- **Compute disclosed:** hundreds of CMA-ES iterations over
  populations of ~50 candidates; per-evaluation cost dominated by
  benchmark runs, not training. Exact GPU-hours not enumerated but
  the paper emphasises that the whole search is feasible on a single
  workstation for 7B-class models.
- **Hyperparameters:** CMA-ES defaults for continuous search; custom
  evolutionary operators for the data-flow-space combinatorial
  search; population size and generation count reported per
  experiment.

## When to cite this paper
Cite Akiba et al. 2024 as the canonical reference for **evolutionary
search over the weights of trained neural networks via merging
recipes**, as opposed to evolutionary search over code (AlphaEvolve,
ShinkaEvolve) or over architecture (NAS). It is also the right
citation for the combined parameter-space plus data-flow-space
merging design and for the empirical claim that cross-domain merging
produces composite capabilities. For practitioners deploying this
technique, cite also the merging-operation papers it builds on
(DARE, TIES, Task Arithmetic) and the specific CMA-ES reference.

## In the knowledge graph
- **Related concepts:** [LLM-driven evolution](../concepts/llm-driven-evolution.md)
  (the "evolve code" counterpart; this paper is the "evolve weights"
  counterpart), [Covariance Matrix Adaptation Evolution Strategy
  (CMA-ES)](../concepts/cma-es.md) (used as the continuous
  optimizer over merging coefficients),
  [parallel-and-distributed-ga](../concepts/parallel-and-distributed-ga.md)
  (multiple evaluations run in parallel)
- **Foundations:** [evolutionary computation](../foundations/evolutionary-computation.md),
  [genetic algorithms](../foundations/genetic-algorithms.md)
- **See also:** [AlphaEvolve](./alphaevolve.md),
  [ShinkaEvolve](./shinkaevolve.md) — both evolve *code*; this paper
  evolves *trained weights*. Complementary rather than competing
  approaches to the same overall "LLM-shaped evolutionary search"
  problem.
