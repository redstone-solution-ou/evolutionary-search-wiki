# Language Model Crossover: Variation through Few-Shot Prompting

> **Short name:** `lmx` · **arXiv:** [2302.12170](https://arxiv.org/abs/2302.12170) · **PDF:** [local](../../papers/lmx_meyerson_2302.12170.pdf) · **Date:** 2023-02 · **Venue:** ACM Transactions on Evolutionary Learning and Optimization 2024

**Authors:** Elliot Meyerson, Mark J. Nelson, Herbie Bradley, Adam Gaier, Arash Moradi, Amy K. Hoover, Joel Lehman (Cognizant AI Labs, American University, Cambridge / CarperAI, Autodesk Research, NJIT)

## Abstract
Language Model Crossover (LMX) is the claim that a sufficiently large
pretrained Large Language Model (LLM), prompted with a small number
of example solutions (parents), naturally produces in-context
outputs that behave like *evolutionary crossover* — the outputs
combine and recombine features of the parents in semantically
coherent ways. LMX is a one-line variation operator: concatenate
the parents into a prompt, feed to any pretrained LLM, parse the
output as offspring. No fine-tuning, no training-time modification,
no domain-specific crossover logic. The paper demonstrates LMX
across six deliberately heterogeneous representations — binary
strings, English sentences, mathematical expressions, image-
generation prompts, and Python code — and establishes that the
technique's versatility is its central contribution: a single
variation operator that works on *any* text-serialisable genotype.

## Key contributions
- Establishes that LLM in-context learning is structurally equivalent
  to an evolutionary crossover operator: given parent examples in a
  prompt, the LLM's output inherits features of all parents in
  coherent ways. This identification — of few-shot prompting with
  evolutionary variation — is the paper's central insight.
- Provides the **simplest-possible implementation**: concatenate
  parents into a prompt, parse LLM output. A few lines of code.
  No fine-tuning, no RL, no auxiliary training signal.
- Demonstrates versatility across **six heterogeneous domains** with
  no domain-specific tweaks: binary strings, English sentence style
  transfer, symbolic regression of mathematical expressions,
  image-generation-prompt evolution, and Python code (Sodarace
  creatures following the Evolution through Large Models setup).
  This breadth is load-bearing for the main claim: LMX works on any
  text-serialisable representation.
- Shows that LMX can be integrated with existing evolutionary
  algorithms (e.g. MAP-Elites) as a drop-in replacement for
  hand-designed crossover, without changing the outer loop.
- Positions LLMs as **general-purpose variation operators** for
  evolutionary computation, not just for text generation — a
  reframing that opens up an entire class of applications where the
  "genome" is any structured text: code, configs, JSON documents,
  YAML pipelines, mathematical expressions, etc.

## Method at a glance
Given a population of text-represented candidates, select `k`
parents (typically 2–10), concatenate them into an LLM prompt with a
separator token between each, and optionally a small instruction
prefix. Feed the prompt through any pretrained LLM (GPT-class,
open-source Llama / Mistral, any will do). Parse the LLM's
continuation as offspring — split on the separator, validate each
chunk as a well-formed member of the representation. Accept valid
offspring into the next generation (optionally under a
fitness-conditioned selection step). No parameters to tune beyond
the LLM's sampling temperature, the number of parents, and how the
parents are ordered in the prompt.

## Why it matters
LMX generalises "LLM as mutation operator" (Evolution through Large
Models, Lehman et al. 2022) to "LLM as crossover operator". It also
establishes a much broader claim: **because the representation is
text, the evolutionary framework becomes representation-agnostic** —
any domain where candidates can be serialised as text is amenable
to LMX without building a new crossover operator from scratch. This
is the theoretical foundation for the recent wave of LLM-driven
evolution work — including [AlphaEvolve](./alphaevolve.md) and
[ShinkaEvolve](./shinkaevolve.md) — which all use LLMs as variation
operators over code, as well as for any future work evolving JSON
configs, YAML pipelines, protobuf schemas, DSL programs, or any
other text-serialisable genotype.

## Strengths
- **Simplicity is the feature.** A few lines of code. No fine-tuning,
  no curriculum, no custom loss. The same operator works across
  radically different domains.
- **Representation-agnostic.** The paper deliberately picks
  maximally diverse domains — binary strings, sentences, math
  expressions, image prompts, code — and shows the same LMX operator
  handles all of them. This is the strongest form of evidence for
  the generality claim.
- **Composes with existing evolutionary algorithms.** The paper
  integrates LMX into MAP-Elites and shows it works as a drop-in
  replacement. No custom outer loop needed; any EA that takes a
  crossover operator accepts LMX.
- **Implementation is public-LLM-friendly.** No OpenAI dependency —
  LMX works with any pretrained LLM including open-source ones,
  democratising the approach.
- **Identifies the in-context-learning → crossover mapping cleanly.**
  Previous work used LLMs to generate code or mutations; LMX is the
  paper that names the mapping between few-shot prompting and
  evolutionary variation, making it a citable primitive rather than
  an implementation detail buried inside system papers.

## Limitations and open critiques
- **LLM call per variation is the cost.** Modern LLMs are not free;
  per-offspring cost is orders of magnitude higher than a classical
  crossover operator (bit-flip, subtree swap). Whether this cost
  amortises against the quality improvement depends on the domain.
  The paper does not provide a comprehensive cost-vs-quality
  frontier analysis.
- **Mode collapse.** LLMs, especially with low sampling temperature,
  can collapse to a narrow subset of the parent-induced distribution.
  Without explicit novelty pressure (which LMX alone does not
  provide), evolutionary diversity can deteriorate over generations.
  Later work — notably ShinkaEvolve — addresses this with
  rejection-sampling gates; LMX does not.
- **Parent ordering matters.** LLM outputs depend on the order of
  the parents in the prompt. The paper reports this as a minor
  effect but does not resolve the principled question of how to
  order parents. For some domains (symbolic regression) ordering
  matters more than for others.
- **Validity of offspring is not guaranteed.** The LLM can produce
  output that is not a valid member of the target representation
  (malformed JSON, uncompilable code, binary strings of wrong
  length). LMX treats validity as a post-hoc filter, which means
  wasted LLM calls on invalid outputs. External validators (JSON
  schema, compiler, type checker) are a standard mitigation but
  add infrastructure.
- **Six domains is a proof-of-concept, not a scaling study.** The
  breadth is impressive but each individual demonstration is small
  scale. Whether LMX supports long-horizon evolutionary runs
  (thousands of generations, tens of thousands of candidates)
  without degrading is not established by this paper and is
  addressed by follow-up work (AlphaEvolve, ShinkaEvolve).

## Follow-up work and dialogue
LMX is the direct theoretical predecessor of the 2025
LLM-driven-code-evolution systems: *"AlphaEvolve: A coding agent for
scientific and algorithmic discovery"* (Novikov et al., DeepMind
2025) and *"ShinkaEvolve: Towards Open-Ended and Sample-Efficient
Program Evolution"* (Lange, Imajuku & Cetin, Sakana AI 2025) — both
use LLM-driven variation operators that are LMX at heart, with
additional machinery (rejection sampling, bandit LLM ensembles,
program databases) layered on top. LMX is also the companion
paper to *"Evolution through Large Models"* (Lehman et al., GPTP
2022, arXiv:2206.08896), which introduced the LLM-as-mutation-
operator framing; LMX makes the parallel argument for crossover.
See also *"LLM Guided Evolution: The Automation of Models Advancing
Models"* (Morris et al., GECCO 2024, arXiv:2403.11446) for a
specific ML-model-code application.

## Reproducibility
- **Open code:** released alongside the paper (linked in §1 of the
  arXiv version).
- **Domains tested:** binary strings, English sentence style
  transfer, symbolic regression on Feynman equations, image prompts
  for a text-to-image model, Sodarace Python creatures.
- **Compute disclosed:** experiments run on GPT-class LLMs (GPT-2,
  GPT-3, OPT variants depending on the experiment). Per-call cost
  depends on the LLM used; the paper emphasises that the technique
  works with open-source small models as well as large closed ones.
- **Hyperparameters:** LLM sampling temperature, number of parents
  per prompt, prompt separator — all relatively robust to exact
  values per the paper's ablations.

## When to cite this paper
Cite LMX as the canonical reference for *"LLM as a general-purpose
crossover operator over any text-serialisable genotype"*. This is
the right citation for any evolutionary loop whose variation
operator is "show the parents to an LLM, parse the output" —
including Python code evolution, DSL search, JSON-config search,
YAML-pipeline search, prompt evolution, and any future
text-representation. For the *mutation* counterpart (single parent
+ LLM), cite *"Evolution through Large Models"* (Lehman et al.
2022). For the full closed-loop systems at scale, cite AlphaEvolve
or ShinkaEvolve.

## In the knowledge graph
- **Related concepts:** [LLM-driven evolution](../concepts/llm-driven-evolution.md)
  (LMX is the foundational method paper; AlphaEvolve and
  ShinkaEvolve build on it),
  [novelty-and-quality-diversity](../concepts/novelty-and-quality-diversity.md)
  (the paper integrates with MAP-Elites; raises the question of
  novelty pressure on top of LMX, which ShinkaEvolve later
  addresses)
- **Foundations:** [evolutionary computation](../foundations/evolutionary-computation.md),
  [genetic algorithms](../foundations/genetic-algorithms.md)
- **See also:** [AlphaEvolve](./alphaevolve.md),
  [ShinkaEvolve](./shinkaevolve.md) — both use LMX-style
  LLM-driven variation operators as their core primitive, with
  additional machinery on top.
