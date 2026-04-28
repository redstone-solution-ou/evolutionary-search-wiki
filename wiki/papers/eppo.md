# Evolutionary Pre-Prompt Optimization for Mathematical Reasoning

> **Short name:** `eppo` · **arXiv:** [2412.04291](https://arxiv.org/abs/2412.04291) · **PDF:** [local](../../papers/eppo_2412.04291.pdf) · **Date:** 2026-02 (v2; original v1 2024-12) · **Venue:** preprint (ACM submission)

**Authors:** Mathurin Videau, Alessandro Leite, Marc Schoenauer, Olivier Teytaud (Meta AI, TAU / INRIA / LISN, Université de Rouen Normandy, Thales).

## Abstract

The paper studies few-shot Chain-of-Thought (CoT) prompt design for mathematical reasoning as a discrete combinatorial optimization problem. Given a fixed Large Language Model (LLM) and a pool of demonstration examples, the goal is to select a small ordered subset (typically 2–16 examples) that, used as a fixed pre-prompt for *all* downstream queries, maximizes the LLM's exact-match accuracy on a target task. The authors introduce **Evolutionary Pre-Prompt Optimization (EPPO)**: a comparison-based black-box evolutionary algorithm — instantiated through Nevergrad — that searches the integer-index space directly. They prove an information-theoretic generalization bound that depends only on the optimizer's feedback arity κ and budget b, not on the ambient pre-prompt space size or the LLM, and demonstrate gains exceeding 10 absolute exact-match points on GSM8k and MathQA with LLaMA2-70B over standard CoT baselines.

## Key contributions

- Frames task-level few-shot CoT prompt selection as a discrete black-box optimization problem over integer indices into a demonstration pool, amenable to off-the-shelf comparison-based evolutionary algorithms (Sections 2.2, 2.4).
- Proves a model-agnostic generalization-risk bound `Pr(|L̂(r) − L(r)| > ε) ≤ κ^b · δ_{1,ε}` (Theorem A.2) that depends only on the optimizer's feedback arity κ and budget b, isolating the *selection-induced* overfitting risk and bounding it independently of the LLM and of the ambient pre-prompt space.
- Empirically demonstrates EPPO 4-shot improvements of +11.4 exact-match points on GSM8k (56.8 → 68.2) and +8.7 exact-match points on MathQA (25.4 → 34.1) for LLaMA2-70B, with statistical significance p < 0.02 across all contexts (Table 1).
- Shows the gain is additive with self-consistency: EPPO 4-shot + self-consistency reaches 79.1% on GSM8k vs 68.3% for CoT + self-consistency (Table 4).
- Robustness analyses (permutation, removal, composition) show 4-shot is the sweet spot, 8-shot pre-prompts contain redundancy that survives the loss of one or two shots, and naive concatenation of two strong 4-shot prompts degrades performance (Section 4, Figures 7–10).

## Method at a glance

EPPO instantiates a single closed loop. A **demonstration set** D of (problem, CoT solution) pairs is built from the task's training data plus LLM-self-generated rationales — up to 50% on GSM8k, 0% on MATH and MathQA — to enlarge diversity and lessen reliance on hand-annotated rationales (Section 2.3). A separate **training set** T is held out to score candidates; the **test set** is sealed throughout. A pre-prompt p is encoded as a length-s integer array `(i_1, …, i_s) ∈ {1, …, |D|}^s` (Section 2.2); the phenotype is the corresponding concatenated few-shot block. At each iteration the optimizer's `Ask` step proposes κ candidate pre-prompts; each is scored by running the fixed LLM on T, and the `Tell` step receives only the *index of the best candidate among the κ* — never a scalar fitness. After b iterations the archived best is returned and evaluated on the test set (Algorithm 1).

The variation operator is whichever Nevergrad optimizer is selected — Discrete (1+1)-ES, Portfolio, DoubleFastGA, Lengler, LogNormal, or recombining variants — adapted to categorical variables by replacing mutation with a uniform redraw from `{1, …, |D|}∖{current value}` (Section 2.4). Discrete (1+1)-ES is the empirical winner for small s on the downsampled regime; Portfolio and DiscLengler are competitive at larger s. Crossover variants (one-point, two-point, uniform) are compared and offer no consistent advantage given the relative size of D versus s.

### How comparison-based feedback bounds the generalization risk

Section 2.6 and Appendix A make this the load-bearing argument of the paper. Because the optimizer at each step ingests only κ-ary feedback (the index of the best of κ candidates), the entire run is determined by a sequence of b κ-ary symbols, hence at most κ^b distinct returnable pre-prompts across all random seeds. A union bound over this finite candidate set, combined with a single-candidate Hoeffding-style concentration bound `δ_{1,ε} = 2 exp(−2Tε²)`, gives `Pr(err_ε(r)) ≤ κ^b · δ_{1,ε}` (Theorem A.2). The bound is independent of |D|, of s, and of the LLM's capacity — it tracks only the *information* the selector consumes. This is the structural reason EPPO favors comparison-based optimizers over Bayesian, gradient, or absolute-fitness methods: comparison-only feedback caps the size of the effective hypothesis class. Random search corresponds to `M = b` candidates and therefore enjoys the strictly tighter `b · δ_{1,ε}` bound — but typically converges to weaker pre-prompts when the budget is large enough to outrun overfitting; the paper presents the trade-off (Equation 10) as the central design tension.

### Why few-shot size matters

Increasing s from 2 to 4 reduces *test* error; pushing further to 8 or 16 *worsens* test exact-match in the downsampled regime (Figure 2, Table 3). The paper attributes this to overfitting: each additional shot multiplies the ambient pre-prompt space as `|D|^s` and the κ^b bound is loose enough that empirical overfitting tracks s in the data-scarce regime. With the full GSM8k training set (Table 4) the overfitting margin shrinks and 8-shot becomes competitive — directly consistent with the bound's `T`-scaling. Removing one or two shots from an optimized 8-shot pre-prompt does not degrade performance; removing them from a 4-shot one does (Figures 8–9): the 8-shot pre-prompts contain redundancy.

## Why it matters

EPPO is the canonical reference for treating *fixed task-level few-shot prompt design* as a black-box evolutionary search problem, with a certifiable generalization bound on the selected prompt. It complements the dominant LLM-driven program-evolution thread (FunSearch, [AlphaEvolve](alphaevolve.md), [ShinkaEvolve](shinkaevolve.md)) by showing that classical discrete EAs still pay off when the LLM is treated as the *fitness oracle* rather than the variation operator — and gives the first information-theoretic argument for *why* coarse, comparison-only feedback should be preferred over fine-grained gradient or absolute-fitness signal in the prompt-engineering setting.

## Strengths

- **Theoretical novelty:** the κ^b · δ_{1,ε} bound is independent of |D| and of the LLM, isolating the selection effect. Such bounds are rare in the prompt-engineering literature.
- **Strong empirical gains:** > 10 absolute exact-match points on GSM8k and MathQA for LLaMA2-70B, with aggregated statistical significance p < 0.02 (Table 1, footnoted methodology in Section 3.1).
- **Compute-cheap relative to fine-tuning:** ~3 hours on 64 V100 GPUs for downsampled GSM8k with LLaMA2-70B at budget 50; no backward pass and a smaller memory footprint than gradient-based fine-tuning (Section 5).
- **Combines additively with self-consistency** (Table 4): EPPO 4-shot + self-consistency reaches 79.1% on GSM8k versus 68.3% for CoT + self-consistency.
- **Honest robustness analysis:** permutation has minor impact, removal exposes 8-shot redundancy, naive composition usually fails (Section 4, Figures 7–10).
- **Optimizer benchmark published:** Discrete (1+1)-ES, Portfolio, DoubleFastGA, Lengler / DiscLengler, LogNormal, recombining variants, and TPE benchmarked against random search (Table 5).

## Limitations and open critiques

- **LLaMA2-only evaluation.** All experiments use LLaMA2-7B and -70B base or chat models (2023 vintage). The paper does not report on LLaMA3, Mistral, Qwen, GPT-4, or Gemini families; whether EPPO's gains survive the post-2024 instruction-tuning generation is unstudied.
- **Statistical significance fails on the smaller model.** For LLaMA2-7B the p-value for outperforming the baseline is only p < 0.39 (Section 3.2 / Table 2): explicitly "not significant." Headline gains are concentrated on the 70B model.
- **Cross-model transfer is asymmetric and weak.** Pre-prompts optimized on 70B do not transfer to 7B; the reverse direction sometimes helps but the model-specific optimum is always strictly better (Section 3.2). EPPO must be re-run per model.
- **Per-example retrieval baselines explicitly excluded.** The work opts out of comparison against retrieval-based In-Context Learning (Liu et al. 2021, Rubin et al. 2022, Su et al. 2023, Wu et al. 2022, Ye et al. 2023, Zhang et al. 2023a), citing scope. The relative cost-benefit versus retrieval is therefore not settled.
- **Compute budget is non-trivial at scale.** The full-GSM8k LLaMA2-70B run used 160 V100 GPUs for ~48 hours (Figure 3 caption). The cost advantage over fine-tuning is real but not extreme.
- **Code release is not announced in the paper body.** Nevergrad and the LLaMA2 weights are public, but a packaged EPPO harness is not advertised, which raises the bar for independent reproduction.

## Follow-up work and dialogue

The closest published cousin is **Promptbreeder** (Fernando, Banarse, Michalewski, Osindero, Rocktäschel, ICML 2024), which the paper explicitly positions as complementary: Promptbreeder uses an LLM to *evolve the structure and content* of prompts, while EPPO uses classical discrete EAs to *select examples from a fixed pool*. The two could be combined, with EPPO selecting demonstration material that Promptbreeder then rewrites. The first author's own follow-up, *Evolutionary Retrofitting* (Videau, Zameshina, Leite, Najman, Schoenauer, Teytaud, arXiv:2410.11330, 2024), extends the comparison-based-EA-on-LLM-input pattern to other settings. Methodologically, EPPO is the *inverse* of the [LMX](lmx.md) → [AlphaEvolve](alphaevolve.md) → [ShinkaEvolve](shinkaevolve.md) lineage: in those papers the LLM is the variation operator and the evaluator is task-specific code; in EPPO the EA is the variation operator and the LLM is the evaluator. The two threads are complementary and could feed each other.

## Reproducibility

- **Open code:** not disclosed in the paper. [Nevergrad](https://github.com/FacebookResearch/Nevergrad) (Rapin & Teytaud 2018) and the LLaMA2 weights are public, but a packaged EPPO harness is not advertised in the manuscript.
- **Domains used:** GSM8k, SVAMP, MathQA, MATH (mathematical-reasoning datasets).
- **Compute disclosed:** "longest run used 16 GPUs during ≃ 30 hours" for downsampled GSM8k; "longest run used 160 GPUs during ≃ 48 hours" for full GSM8k (LLaMA2-70B; Figure 3 caption). LLaMA2-7B runs are reported as "less than one hour … with float16 precision and a budget of 50" (Section 5).
- **Hyperparameters:** budget `b ∈ {50, 100, 150}`; few-shot size `s ∈ {2, 4, 8, 12, 16}`; comparison arity κ = 2 in the main experiments; demonstration set ~1000 (down from 7473 for GSM8k); training set ~400–520 examples; test set 1000–5000 depending on dataset (Table 6). Demonstration set is up to 50% LLM-self-generated for GSM8k, 0% for MathQA and MATH. Self-consistency uses temperature τ = 0.6 with `maj@8`.

## When to cite this paper

Cite EPPO as the canonical reference for *task-level evolutionary few-shot example selection* with a comparison-based EA over an integer-genome representation, and for the *information-theoretic generalization bound under κ-ary feedback* (Theorem A.2). It is the right citation when arguing that classical discrete evolutionary algorithms remain competitive in the LLM era specifically because comparison-only feedback is theoretically certifiable, and when contrasting fixed-pre-prompt optimization with per-example retrieval-based In-Context Learning.

## In the knowledge graph

- **Related concepts:** [llm-driven-evolution](../concepts/llm-driven-evolution.md) (the inverted pattern: LLM as evaluator rather than mutator), [parallel-and-distributed-ga](../concepts/parallel-and-distributed-ga.md) (the broader discrete-EA family from which Discrete (1+1)-ES descends).
- **Foundations:** [evolutionary-computation](../foundations/evolutionary-computation.md), [genetic-algorithms](../foundations/genetic-algorithms.md).
- **See also:** [LMX](lmx.md) (LLM-as-crossover, the methodological inverse), [AlphaEvolve](alphaevolve.md) (LLM-as-mutator at scale), [ShinkaEvolve](shinkaevolve.md) (sample-efficient LLM-as-mutator), [Grammatical Evolution](grammatical-evolution.md) (the classical discrete-EA-on-text pattern with a hand-written grammar).
