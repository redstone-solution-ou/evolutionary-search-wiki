# EvoPrompt: Connecting LLMs with Evolutionary Algorithms Yields Powerful Prompt Optimizers

> **Short name:** `evoprompt` · **arXiv:** [2309.08532](https://arxiv.org/abs/2309.08532) · **PDF:** [local](../../papers/evoprompt_2309.08532.pdf) · **Date:** 2023-09 (v1; v3 2025-05) · **Venue:** ICLR 2024

**Authors:** Qingyan Guo, Rui Wang, Junliang Guo, Bei Li, Kaitao Song, Xu Tan, Guoqing Liu, Jiang Bian, Yujiu Yang (Tsinghua University, Microsoft Research, Northeastern University).

## Abstract

EvoPrompt is a discrete prompt-optimization framework that connects Large Language Models (LLMs) with classical evolutionary algorithms (EAs). The authors observe that while LLMs are competent at generating coherent natural language but struggle to balance exploration and exploitation in optimization, EAs are derivative-free black-box optimizers with strong exploration/exploitation properties but fail on discrete-text representations because their token-level operators ignore the coherence constraints of natural language. EvoPrompt resolves the tension by using LLMs *as* the evolutionary operators — instructed via natural-language templates to perform mutation and crossover on candidate prompts — while keeping the EA scaffold (selection, fitness scoring on a development set, population update) in classical form. The paper instantiates EvoPrompt with two algorithms — Genetic Algorithm (GA, with roulette-wheel selection) and Differential Evolution (DE, with mutation on identified "different parts" between two parents) — and reports gains over manual instructions, PromptSource, Natural Instructions, APE, and APO on 31 datasets covering language understanding, generation, and Big-Bench Hard, with up to 25% improvement on individual BBH tasks and a 3.5% average BBH gain for the DE variant.

## Key contributions

- Frames discrete prompt optimization as a black-box EA where the variation operator is itself an LLM, instructed in natural language to perform mutation or crossover (Section 3, Algorithm 1).
- Instantiates the framework with two distinct EAs — **EvoPrompt (GA)** with roulette-wheel selection and a two-step `crossover then mutate` LLM template (Section 3.2, Figure 1), and **EvoPrompt (DE)** with a four-step `identify-different-parts → mutate-differences → combine-with-current-best → crossover-with-basic-prompt` template (Section 3.3, Figure 2) — and shows the DE variant generally wins on harder generation and Big-Bench Hard tasks while the GA variant edges out on simpler classification.
- Empirical evaluation on 31 datasets across 7 language-understanding tasks (SST-2, CR, MR, SST-5, AG's News, TREC, Subj), 2 generation tasks (SAMSum summarization, ASSET text simplification) and 23 Big-Bench Hard tasks, on both Alpaca-7B and GPT-3.5 (text-davinci-003) — establishing that the framework transfers across closed-source and open-source models (Section 4, Tables 1–3, Figure 3).
- Ablations of the three load-bearing design choices: the LLM-mutation-on-different-parts trick from DE (Section 5.2, Table 5), the choice of selection strategy (Section 5.1, Table 4: roulette wheel beats random and tournament), and the role of initial-population quality (Section 5.3, Table 6: DE is more robust to a low-quality initial population than GA).
- Releases the discovered optimal prompts and an open-source implementation at [github.com/beeevita/EvoPrompt](https://github.com/beeevita/EvoPrompt).

## Method at a glance

EvoPrompt instantiates the standard EA loop (Algorithm 1): an initial population of N prompts P₀ = {p₁, …, p_N} is scored on a development set D using `f_D(·)` — the LLM's task accuracy or task-appropriate metric (ROUGE for summarization, SARI for simplification, exact-match for BBH); each iteration t = 1 … T applies a *selection* step to pick parents, an LLM-call *evolution* step to produce a child prompt p′ via the templated mutation/crossover instruction `Evo(·)`, an *evaluation* step to score the child on D, and an *update* step that retains the top-N performing prompts. The final returned prompt is the best of the final population (Section 3.1).

The two instantiations differ in their `Evo(·)` template and selection rule. **EvoPrompt (GA)** picks two parents by roulette-wheel selection (probability `p_i = s_i / Σ s_j`) and prompts the LLM with a two-step instruction: "Cross over the following prompts and generate a new prompt", then "Mutate the prompt generated in Step 1 and generate a final prompt bracketed with `<prompt>` and `</prompt>`" (Figure 1). **EvoPrompt (DE)** sequentially walks each population member as the *base* vector x and constructs a candidate by: (1) identifying "different parts" between two randomly-selected parents (the `b − c` step in DE); (2) mutating those different parts (the `F(b − c)` step); (3) combining with the current best prompt (the `a + F(b − c)` step where a is the population's current best); (4) crossing over the result with the base vector x (Figure 2, Section 3.3). The first three steps are folded into a single LLM call with a four-stage instruction template.

### Why mutation-on-different-parts is the load-bearing DE choice

Section 5.2 and Table 5 make this the explicit load-bearing argument for the DE variant. A naive port of DE to natural language would mutate *all* tokens of the difference vector, but the authors observe that the *shared* components of two prompts in the current population are likely already good (they survived several rounds of selection); only the *different* components carry the population's remaining variance. Restricting LLM mutation to those different parts therefore preserves population-level invariants while exploring on the genuinely uncertain axis. The ablation contrasts four configurations — `Diff/best` (the EvoPrompt design), `All/best`, `Diff/random`, and `Diff/eliminate` (skipping the cross-with-best step) — and shows that mutating only the different parts is consistently best on both Subj (75.55) and ASSET (46.21), with `All` (mutating everything) dropping to 69.87 / 45.73 on the same tasks.

### Why the selection strategy and population quality are reported

Section 5.1's Table 4 ablation between random selection, tournament, and roulette wheel is included specifically to argue against a "the LLM doesn't care which two parents you give it" reading of the framework — roulette wheel beats random by ~0.7 absolute points and tournament by ~0.2 points on the SST-5/ASSET average, which the paper takes as evidence that *fitness-proportional* parent sampling matters even when the variation operator is a learned model. Section 5.3's Table 6 ablation across initial populations (top-10, bottom-10, random-10, with and without LLM-resampled variations) shows that DE is markedly more robust to a low-quality initial population than GA — when starting from "bottom-10" (worst-performing crafted prompts), DE retains 48.64% on SST-5 against GA's 47.80%; when starting from manually curated "top-10" prompts the two are roughly tied. The implied design guidance: prefer DE when initial prompt quality is low or unknown, prefer GA when good seed prompts are available.

## Why it matters

EvoPrompt is the cleanest reference for *general-purpose* discrete prompt optimization with LLMs as the evolutionary operators — the framework is explicit about which classical EA is being instantiated (GA, DE), it benchmarks both on a wide dataset suite, and it ablates the load-bearing design choices (selection strategy, initial population quality, mutation-on-different-parts). It is also the most-cited demonstration that the *choice* of underlying classical EA matters when the variation operator is an LLM, with DE consistently outperforming GA on harder tasks and on low-quality initial populations.

## Strengths

- **Wide empirical coverage:** 31 datasets across language understanding (7), language generation (2), and Big-Bench Hard (22), on two distinct LLMs (Alpaca-7B and GPT-3.5) — among the broadest evaluations in the prompt-evolution literature.
- **Two distinct EA instantiations cleanly separated:** the paper does not merely use "an EA" generically; it reports GA and DE as separate methods with separate templates and provides head-to-head comparisons (Tables 1–3, Figure 3).
- **Up to 25% improvement on individual BBH tasks** and a 3.5% average gain for DE on the BBH suite (Section 4.4).
- **Honest ablations:** mutation-on-different-parts (Table 5), selection strategy (Table 4), initial-population quality (Table 6) — each shows that the design choice it tests is not epiphenomenal.
- **Open-source release** at [github.com/beeevita/EvoPrompt](https://github.com/beeevita/EvoPrompt) including the discovered optimal prompts — a clear advantage over Promptbreeder's closed-PaLM-2-L setting.

## Limitations and open critiques

- **2023-vintage models only.** Headline numbers are reported on Alpaca-7B and GPT-3.5 (text-davinci-003); GPT-4, LLaMA-2/3, Mistral, Qwen, Claude, Gemini, and any 2024+ model are not evaluated. Whether EvoPrompt's gains survive the modern instruction-tuned generation is not addressed.
- **No self-reference or hyper-mutation.** Unlike Promptbreeder, EvoPrompt evolves task-prompts only — the templates that instruct the LLM to mutate/crossover are hand-designed and held fixed. The paper acknowledges this implicitly by treating the templates as part of the framework definition.
- **Development-set fitness signal can be expensive.** Each generation evaluates N candidate prompts on the dev set, which for some BBH tasks means hundreds of LLM calls per candidate; the paper reports `N` and `T` chosen "based on budget limitation" without a concrete cost figure.
- **Single-seed runs on GPT-3.5.** Results on Alpaca are averaged over 3 random seeds with reported standard deviations, but GPT-3.5 results are reported with one seed "due to budget limitation" (Section 4.1) — limiting the statistical force of the GPT-3.5 numbers.
- **Discrete prompts only.** Soft prompts and prefix tuning are explicitly excluded as out of scope; the framework as stated cannot evolve continuous embeddings.
- **No information-theoretic generalization analysis** of the selection process — the paper is empirical-comparison-driven rather than theoretical, in contrast to [EPPO](eppo.md)'s `κ^b · δ_{1,ε}` bound.

## Follow-up work and dialogue

EvoPrompt is concurrent with [Promptbreeder](promptbreeder.md) (Fernando et al., 2023 / ICML 2024) and **OPRO** (Yang et al., ICLR 2024); the three define the modern wave of LLM-driven prompt optimization. Of the three, EvoPrompt is the most explicit about the choice of classical EA and the most diversified across tasks, Promptbreeder adds a self-referential layer (mutation-prompts evolve too) but stays on a single closed model, and OPRO uses a single fixed mutation prompt and is less explicitly evolutionary. The methodological inverse — classical comparison-based EAs over an integer-genome with the LLM as fitness oracle — is [EPPO](eppo.md) (Videau et al., 2024). The general "LLM as variation operator" thread that EvoPrompt instantiates was foundationally established by [LMX](lmx.md) (Meyerson et al., 2023 / ACM TELO 2024) and scaled to programs by FunSearch, [AlphaEvolve](alphaevolve.md), and [ShinkaEvolve](shinkaevolve.md).

## Reproducibility

- **Open code:** yes, [github.com/beeevita/EvoPrompt](https://github.com/beeevita/EvoPrompt). Discovered optimal prompts released alongside.
- **Domains used:** SST-2, CR, MR, SST-5, AG's News, TREC, Subj (language understanding); SAMSum (summarization), ASSET (text simplification); 22 Big-Bench Hard tasks (1 dropped because manual prompt already saturates at 100%).
- **Compute disclosed:** not in the body of the paper. Number of LLM calls per run is bounded by `population size N × number of iterations T` (each requiring one LLM mutation call and N dev-set evaluations); concrete N and T values are noted as "set based on budget limitation" (Section 4.1) without dollar or wall-clock figures.
- **Hyperparameters:** population size N and number of iterations T not pinned to a single value across tasks (chosen per-task by budget). Selection: roulette wheel (GA) by default; ablation against random and tournament in Table 4. Initial population: 10 manual prompts plus LLM-resampled variations (Section 5.3 ablates this). Underlying LLMs: Alpaca-7B (open-source, results averaged over 3 seeds) and GPT-3.5 (text-davinci-003, single seed).

## When to cite this paper

Cite EvoPrompt as the canonical reference for *general-purpose* LLM-driven discrete prompt optimization with a classical EA scaffold (specifically GA or DE), and for the empirical demonstration that the choice of underlying EA matters when the variation operator is an LLM (DE outperforms GA on harder tasks and low-quality initial populations). It is also the right citation for the *mutation-on-different-parts* design choice in DE-on-text and for the broad transfer claim across closed-source (GPT-3.5) and open-source (Alpaca-7B) language models.

## In the knowledge graph

- **Related concepts:** [evolutionary-prompt-optimization](../concepts/evolutionary-prompt-optimization.md) (EvoPrompt is the canonical reference for the GA/DE-flavoured LLM-as-mutator subpattern), [llm-driven-evolution](../concepts/llm-driven-evolution.md) (EvoPrompt instantiates the LLM-as-variation-operator pattern with explicit GA and DE algorithm choices), [parallel-and-distributed-ga](../concepts/parallel-and-distributed-ga.md) (the GA selection-and-population machinery EvoPrompt borrows from).
- **Foundations:** [evolutionary-computation](../foundations/evolutionary-computation.md), [genetic-algorithms](../foundations/genetic-algorithms.md) (EvoPrompt is a direct port of the classical GA/DE templates with LLM substitution for variation).
- **See also:** [Promptbreeder](promptbreeder.md) (concurrent self-referential cousin on a closed model), [EPPO](eppo.md) (the inverse pattern: classical EA + LLM-as-fitness-oracle), [LMX](lmx.md) (the foundational "LLM-as-variation-operator" reference EvoPrompt cites for legitimacy).
