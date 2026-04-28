# Promptbreeder: Self-Referential Self-Improvement via Prompt Evolution

> **Short name:** `promptbreeder` · **arXiv:** [2309.16797](https://arxiv.org/abs/2309.16797) · **PDF:** [local](../../papers/promptbreeder_2309.16797.pdf) · **Date:** 2023-09 · **Venue:** preprint (later ICML 2024)

**Authors:** Chrisantha Fernando, Dylan Banarse, Henryk Michalewski, Simon Osindero, Tim Rocktäschel (Google DeepMind).

## Abstract

Promptbreeder is a general-purpose, self-referential prompt-evolution system: an evolutionary loop in which a Large Language Model (LLM) acts as the variation operator that mutates *task-prompts* used to elicit reasoning, and *also* acts as the variation operator that mutates the *mutation-prompts* used to drive its own search. Driven by PaLM 2-L, Promptbreeder evolves a population of (task-prompt, mutation-prompt) pairs over 20–30 generations of binary-tournament selection. Mutation operators fall into five classes — direct mutation, estimation-of-distribution mutation, hyper-mutation of the mutation-prompt itself, Lamarckian mutation reverse-engineering a task-prompt from a working-out, and prompt crossover plus context shuffling — exploiting the empirical fact that LLMs can be prompted to act as competent mutation operators. The paper reports state-of-the-art zero-shot results on GSM8K (83.9%), SVAMP, AQuA-RAT, and ETHOS hate-speech classification, surpassing Chain-of-Thought, Plan-and-Solve, and OPRO on identical underlying LLMs.

## Key contributions

- Frames prompt design as a **self-referential** evolutionary process: not only the task-prompts are evolved, but also the mutation-prompts that govern how task-prompts are mutated, via an additional class of **hyper-mutation** operators conditioned on a hyper-mutation prompt H (Section 3.2.3).
- Defines a unit of evolution as a (task-prompt, mutation-prompt) pair — and in the few-shot case, also a set of correct workings-out — kept in 1:1 correspondence so that mutation-prompts can specialise to their associated task-prompts (Section 3, Section 3.1).
- Catalogues nine concrete LLM-driven mutation operators across five classes (direct, estimation-of-distribution, hyper-mutation, Lamarckian, crossover/context-shuffling) and demonstrates via ablation (Appendix L) that removing any self-referential operator hurts performance on at least one benchmark (Section 5).
- Reports zero-shot 83.9% on GSM8K with the unintuitive evolved prompt `"SOLUTION:"`, surpassing OPRO's 80.2% on the same LLM (PaLM 2-L) and beating hand-designed Chain-of-Thought, Plan-and-Solve, and Auto-CoT across MultiArith, SingleEq, AddSub, SVAMP, AQuA-RAT, GSM8K, CommonsenseQA, and StrategyQA (Section 5, Table 1).
- Demonstrates evolved domain-specific prompts on the ETHOS Hate-Speech Classification problem (89% vs 80% for the hand-designed `"Determine whether a text contains hate speech"`), showing the framework adapts to non-mathematical domains.

## Method at a glance

A unit of evolution consists of a task-prompt P, a mutation-prompt M, and (in the few-shot case) a list of correct workings-out used as in-context examples. The fitness of a unit is the proportion of correct answers an LLM gives to a random batch of 100 (question, answer) pairs from the training set when conditioned on P (Section 3). A binary tournament (Harvey 2011) is run on a population of 50 units for 20–30 generations: two units are sampled, the higher-fitness one is mutated by one of nine operators, and its mutated copy overwrites the loser. The mutation operator is itself an LLM call: a mutated task-prompt is `P' = LLM(M + P)`; a mutated mutation-prompt is `M' = LLM(H + M)` where H is a hyper-mutation prompt. Initialization concatenates a random "thinking-style" (e.g. "Let's think step by step"), a random mutation-prompt (e.g. "Make a variant of the prompt."), and a domain-specific problem description, asking the LLM to produce a continuation — yielding two diverse initial task-prompts per unit (Section 3.1). The result is a population of compositional, domain-adapted task-prompts whose mutation-prompts have themselves co-evolved.

### How self-reference is implemented

Section 3.2.3 makes the self-reference concrete. The five **direct** mutation classes — direct mutation, estimation-of-distribution (EDA) mutation with a BERT-cosine-similarity diversity filter, EDA-rank-and-index mutation that biases the LLM toward the *end* of an ascending fitness list (the paper deliberately "lies" to the LLM about the ordering to break its recency bias), lineage-based mutation conditioning on the unit's elite history, and Lamarckian working-out-to-task-prompt mutation — operate only on task-prompts. The two **hyper-mutation** classes (`zero-order` and `first-order`) instead replace M itself with `LLM(H + M)`. The paper argues (Section 3.2 preamble) that this matters because it lets evolvability itself evolve: the system not only finds better task-prompts but better *ways of looking for* better task-prompts — analogous in spirit to Schmidhuber's self-referential weight matrices (1993, 2003) but with natural language as the substrate, sidestepping any parameter update of the underlying LLM.

### Why the diversity-maintenance machinery is needed

Section 3.2.2 explicitly motivates the EDA mutation's BERT-similarity filter: without it, the LLM is biased toward producing copies of population members already in the prompt list (a recency-and-frequency artefact of token-level autoregressive sampling). The 0.95 cosine-similarity rejection threshold and the deliberate ascending-then-labelled-descending re-ordering trick are both responses to this. Removing either the BERT filter or the re-ordering trick collapses the population's behavioural diversity within a few generations (Appendix L ablations).

## Why it matters

Promptbreeder is the canonical reference for *self-referential* prompt evolution and for the broad observation that LLMs are competent enough at instruction-following to act as mutation operators on natural-language genomes. It establishes empirically that hand-engineered prompt strategies (Chain-of-Thought, Plan-and-Solve, Auto-CoT, OPRO) can be surpassed by automated evolutionary search on the same downstream LLM, and it is the precursor of the broader Large-Language-Model-as-evolutionary-operator pattern that [LMX](lmx.md), [AlphaEvolve](alphaevolve.md), and [ShinkaEvolve](shinkaevolve.md) later scaled to programs rather than prompts.

## Strengths

- **Self-referential mechanism is novel and ablated:** removing any single self-referential class hurts performance somewhere in the benchmark suite (Section 5, Appendix L), evidence that the self-reference is doing real work rather than just contributing diversity.
- **Strong empirical gains across nine benchmarks:** 83.9% zero-shot GSM8K, 71.8% StrategyQA, 85.4% CommonsenseQA, 90.2% SVAMP, all on PaLM 2-L; 89% on ETHOS hate-speech classification (Table 1, Appendix J).
- **Generalises beyond reasoning to classification tasks:** the ETHOS result shows the framework is not narrowly fitted to mathematical reasoning, and the evolved prompts in those domains read as task-specific, not generic.
- **Diversity machinery is principled:** BERT-similarity rejection, ascending/descending-order trick, and lineage-based conditioning all carry explicit rationales tied to LLM-sampling pathologies.
- **No parameter updates required:** the entire system runs at inference time only, sidestepping the question of how to scale fine-tuning across ever-larger LLMs.

## Limitations and open critiques

- **PaLM 2-L only.** All headline numbers are reported against a closed Google model from 2023; the paper does not test on GPT-4, LLaMA, Mistral, or Gemini. Whether the *self-referential* gains are specific to PaLM 2-L is unstudied.
- **Topology of prompting is fixed.** Section 6 acknowledges this: Promptbreeder evolves prompt *content*, not the prompting *algorithm* (e.g. the two-prompt sequential structure, the choice between zero-shot and few-shot, the use of voting). The space of possible reasoning topologies is held constant by hand.
- **Closed-source model and undisclosed inference settings.** PaLM 2-L is not publicly available; sampling temperature, top-p, and per-call cost are not reported in detail. Direct replication is therefore impossible without Google access.
- **Compute cost is not quantified.** With population 50 and 20–30 generations, each generation requiring batched LLM calls for both fitness evaluation (100 Q&A per unit) and mutation, the inference budget is large but not given a concrete dollar or GPU-hour figure.
- **Diminishing returns on simpler tasks.** Several datasets in Table 1 (e.g. SingleEq) saturate near 100%; gains over Plan-and-Solve are tiny. The headline gains are concentrated on the harder benchmarks (GSM8K, AQuA-RAT, StrategyQA).
- **The "SOLUTION:" result is presented but not deeply analyzed.** That a one-word task-prompt outperforms long Plan-and-Solve scaffolds is an intriguing finding; the paper notes it (Section 5) but does not investigate why.

## Follow-up work and dialogue

Concurrent and follow-up work in the same space includes **EvoPrompt** (Guo et al., ICLR 2024, arXiv:2309.08532), which uses LLMs as Genetic-Algorithm or Differential-Evolution operators on prompts but does *not* evolve the mutation-operator itself; **OPRO** (Yang et al., ICLR 2024), which uses a single fixed mutation prompt and is explicitly compared in Table 1; and [EPPO](eppo.md) (Videau et al., 2024), which is the *methodological inverse* — classical comparison-based EAs over an integer-index genome with the LLM as fitness oracle, rather than LLM-as-mutator on a natural-language genome. Promptbreeder also positions itself as the prompt-evolution counterpart of [LMX](lmx.md): both rest on the observation that LLMs make competent variation operators, but LMX targets arbitrary text-serialisable artefacts (programs, JSON, sentences) while Promptbreeder targets prompts and adds the self-referential layer. The same observation later scaled to programs in FunSearch, [AlphaEvolve](alphaevolve.md), and [ShinkaEvolve](shinkaevolve.md).

## Reproducibility

- **Open code:** not released by the authors. Several open-source community reimplementations exist but are not endorsed in the paper.
- **Domains used:** GSM8K, SVAMP, MultiArith, AddSub, AQuA-RAT, SingleEq (arithmetic); CommonsenseQA, StrategyQA (commonsense); ETHOS (hate-speech classification); plus instruction-induction tasks from Honovich et al. 2023.
- **Compute disclosed:** "We used a population size of 50 units, evolved for typically 20–30 generations" (Section 4). No GPU-hours, dollar cost, or wall-clock figure is given.
- **Hyperparameters:** population 50, 20–30 generations, fitness evaluation on 100 random training Q&A pairs, BERT-similarity rejection threshold 0.95 (Section 3.2.2), prompt crossover with 10% probability, context shuffling with 10% probability and a length-inverse re-sample rate (Section 3.2.5). Underlying LLM: PaLM 2-L (Anil et al. 2023). Initial mutation-prompts and thinking-styles enumerated in Appendices C, D, G.

## When to cite this paper

Cite Promptbreeder as the canonical reference for *self-referential* prompt evolution — both task-prompts and the mutation-prompts that drive their evolution are subject to selection and variation — and for the empirical demonstration that automated evolutionary prompt search can surpass hand-engineered Chain-of-Thought, Plan-and-Solve, and OPRO on identical downstream LLMs. It is also the right citation for the unintuitive `"SOLUTION:"` GSM8K result and for the broader claim that natural language can serve as the substrate for self-referential self-improvement without parameter updates.

## In the knowledge graph

- **Related concepts:** [llm-driven-evolution](../concepts/llm-driven-evolution.md) (Promptbreeder applies the LLM-as-mutator pattern to natural-language prompts and adds a self-referential layer on top), [novelty-and-quality-diversity](../concepts/novelty-and-quality-diversity.md) (the BERT-cosine diversity filter is a quality-diversity-flavoured response to LLM mode collapse).
- **Foundations:** [evolutionary-computation](../foundations/evolutionary-computation.md), [genetic-algorithms](../foundations/genetic-algorithms.md) (binary-tournament selection on a fixed-size population is the classical GA template Promptbreeder instantiates).
- **See also:** [LMX](lmx.md) (the LLM-as-variation-operator foundation), [EPPO](eppo.md) (the inverse pattern: classical EA + LLM-as-fitness-oracle on the same prompt-design problem), [AlphaEvolve](alphaevolve.md) and [ShinkaEvolve](shinkaevolve.md) (the program-evolution scale-ups of the LLM-as-mutator pattern).
