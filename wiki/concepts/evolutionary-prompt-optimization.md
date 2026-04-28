# Evolutionary Prompt Optimization

The application of population-based evolutionary search to the
*prompt* — the natural-language string handed to a fixed Large Language
Model (LLM) at inference time — with fitness defined by the LLM's
downstream task performance under the prompt. The prompt is the
genome; the search either mutates it directly with an LLM-as-variation
operator or selects it from a curated pool of demonstrations with
classical discrete EAs.

## Intuition

Prompt design dominates downstream LLM performance. Hand-engineered
strategies — Chain-of-Thought, Plan-and-Solve, "Let's think step by
step", carefully chosen few-shot exemplars — produce wildly different
results on the same task and the same model, and the search for a good
prompt is typically done by humans, with the human in the inner loop
of a long trial-and-error cycle. Evolutionary prompt optimization
removes the human from that inner loop. A population of candidate
prompts is held in memory; each is scored by running the fixed LLM on
a development set; selection and variation produce the next generation;
the loop terminates when budget runs out and returns the best prompt.
Two complementary instantiations exist. In the first, the LLM is
*outside* the search loop as the fitness oracle and a classical
discrete evolutionary algorithm is the variation operator over an
integer-valued genome (typically a list of indices into a curated
demonstration pool); this is the [EPPO](../papers/eppo.md) pattern. In
the second, the LLM is *itself* the variation operator, instructed in
natural language to mutate or recombine candidate prompts that are
held as natural-language strings; this is the
[Promptbreeder](../papers/promptbreeder.md) /
[EvoPrompt](../papers/evoprompt.md) pattern. Both leave the LLM's
parameters untouched — no gradient, no fine-tuning, no parameter
updates — and both inherit their advantages over gradient-based
fine-tuning from that fact.

## Mechanics

The shared template:

```
population = init_prompts(N)            # natural-language strings or integer-index arrays
scores    = [evaluate(p, dev_set) for p in population]
for t in 1..T:
    parents = select(population, scores)              # roulette / tournament / κ-ary comparison
    child   = vary(parents)                           # LLM(M + parents) OR classical mutation
    s_child = evaluate(child, dev_set)
    population, scores = update(population, scores, child, s_child)  # top-N retention
return argmax(population, scores)
```

The two subpatterns differ in three components — the genome, the
variation operator, and the form of the feedback the optimizer
consumes:

| Component | LLM-as-mutator subpattern | Classical-EA subpattern |
|---|---|---|
| Genome | natural-language string | integer-valued array (indices into demo pool) |
| Variation operator | `LLM(template + parents)` | classical mutation / crossover (e.g. Discrete (1+1)-ES) |
| Feedback | absolute fitness on dev set | κ-ary comparison-only (typically) |
| Self-reference | optional (Promptbreeder evolves the mutation prompt) | not yet seen in published work |
| Generalization argument | empirical only | information-theoretic bound `κ^b · δ_{1,ε}` (EPPO) |

The selection rule is largely orthogonal: roulette-wheel selection,
binary tournament, κ-ary comparison, or sequential walk-each-base-
vector (Differential Evolution) all work in either subpattern. The
update rule is typically top-N retention, mirroring elitist GA
defaults.

## Why it works

Three properties make evolutionary prompt optimization work where
hand-tuning fails. **Prompts have rugged, non-smooth fitness
landscapes** — the EvoPrompt paper reports up to 25% absolute swings on
individual Big-Bench Hard tasks across syntactically similar
prompts — so population-based search with diversity preservation beats
local-improvement heuristics. **LLMs are competent variation operators
on natural-language genomes**: a modern LLM, instructed in natural
language to "mutate this prompt slightly" or "combine these two
prompts", produces children that are coherent, grammatically valid,
and semantically related to the parents — the same observation
[LMX](../papers/lmx.md) established for arbitrary text-serialisable
artefacts in 2023, specialised here to the prompt sub-domain. **Coarse
feedback bounds the effective hypothesis class**: when the optimizer
consumes only κ-ary comparison feedback (EPPO's design choice), the
returned prompt is selected from a candidate set of size at most κ^b
where b is the budget, which gives an information-theoretic
generalization bound independent of the prompt-space size and the LLM
itself (EPPO Theorem A.2).

## Trade-offs and failure modes

- **Cost per evaluation is set by the LLM, not the optimizer.** Each
  fitness call requires running the LLM on the development set;
  optimizer iterations are cheap by comparison. A two-LLM-call
  iteration (one mutation, N evaluations) on Alpaca-7B is fast; the
  same on GPT-4 is expensive.
- **Cross-model transfer is weak.** Prompts optimized on a 70-billion-
  parameter model do not transfer to a 7-billion-parameter model
  (EPPO Section 3.2 documents the asymmetry; Promptbreeder is
  PaLM 2-L-only and does not study transfer; EvoPrompt does not
  systematically test it). A prompt optimized for one LLM is at best a
  warm-start for another.
- **Per-task overfitting.** All three exemplar papers fit a separate
  prompt per task and per model. There is no shared "good prompt
  prior" learnt across tasks.
- **LLM-as-mutator pipelines have no theoretical generalization
  bound.** They consume absolute fitness values from the development
  set and have no analogue of EPPO's κ^b bound; the only safeguard
  against overfitting is the size of the development set.
- **Prompt length tends to grow without explicit penalisation.** The
  LLM-as-mutator subpattern can produce ever-longer prompts; without a
  length regularizer or token budget, this is a slow drift toward
  context-window exhaustion.

## Design choices in the literature

- **Genome representation.** Natural-language string
  ([Promptbreeder](../papers/promptbreeder.md),
  [EvoPrompt](../papers/evoprompt.md)) versus integer-valued array
  indexing a curated demonstration pool ([EPPO](../papers/eppo.md)).
- **Variation operator.** A single LLM call wrapped in a
  natural-language template (Promptbreeder, EvoPrompt) versus
  classical discrete EA operators from the Nevergrad library
  (EPPO: Discrete (1+1)-ES, Portfolio, DiscLengler, LogNormal).
- **Self-reference.** Promptbreeder evolves not only task-prompts but
  the *mutation-prompts* that drive task-prompt mutation (`LLM(H + M)`
  for the hyper-mutation step), via a fifth class of mutation
  operators. EvoPrompt and EPPO hold the variation template fixed.
- **Selection.** Binary tournament (Promptbreeder), roulette wheel
  (EvoPrompt GA), sequential walk-each-base-vector
  (EvoPrompt DE), comparison-based κ-ary feedback (EPPO).
- **Initial population.** Promptbreeder uses random combinations of
  thinking-styles and mutation-prompts; EvoPrompt uses curated manual
  prompts plus LLM-resampled variations and ablates the choice (DE is
  more robust to a low-quality initial population than GA); EPPO
  draws integer indices uniformly into a demonstration pool that
  itself includes up to 50% LLM-self-generated rationales.
- **Diversity maintenance.** Promptbreeder applies a BERT-cosine-
  similarity filter (rejection threshold 0.95) to the
  estimation-of-distribution mutation operator and a deliberate
  ascending/descending-order trick to break LLM recency bias; EvoPrompt
  does not have an explicit diversity mechanism beyond population
  retention; EPPO does not need one because the integer-index genome
  is naturally diverse under uniform mutation.
- **Generalization control.** Information-theoretic bound
  (EPPO: `Pr(|L̂(r) − L(r)| > ε) ≤ κ^b · δ_{1,ε}`); empirical
  development-set scoring with no formal guarantee
  (Promptbreeder, EvoPrompt).

## Open questions

- When does LLM-as-mutator beat classical-EA + LLM-as-fitness-oracle?
  The two subpatterns have only been compared head-to-head implicitly
  (across papers, not within a single experiment), and the literature
  does not yet have a clear cost-quality frontier.
- Can self-reference (Promptbreeder's hyper-mutation) be combined with
  EPPO's comparison-only feedback to inherit both the self-improvement
  and the generalization-bound properties?
- How should evolutionary prompt optimization scale to long prompts
  (multi-thousand-token system prompts, agentic scaffolds)? Current
  exemplars all evolve short prompts (≤ a few hundred tokens).
- Cross-model transfer is weak across all three exemplars. What
  mechanisms — adversarial training across multiple LLMs in the
  fitness loop, length normalisation, prompt distillation — would
  produce model-portable prompts?
- Is there a useful coevolutionary version of evolutionary prompt
  optimization — two coevolving populations of (prompt, evaluation
  rubric) pairs, or two prompt populations targeting an adversarial
  judge LLM?

## Papers that exemplify this

- [EPPO](../papers/eppo.md) — Videau, Leite, Schoenauer & Teytaud,
  2024 / 2026. Classical comparison-based EAs from Nevergrad over an
  integer-index genome of few-shot Chain-of-Thought examples. The
  reference for the *information-theoretic* subpattern with a
  certifiable generalization bound.
- [Promptbreeder](../papers/promptbreeder.md) — Fernando, Banarse,
  Michalewski, Osindero & Rocktäschel, Google DeepMind 2023 / ICML
  2024. LLM-as-mutator over natural-language genomes, with the
  *self-referential* twist that mutation-prompts themselves are
  evolved. The reference for self-referential prompt evolution and
  for the empirical demonstration that automated search can surpass
  hand-engineered Chain-of-Thought, Plan-and-Solve, and OPRO on
  identical underlying LLMs.
- [EvoPrompt](../papers/evoprompt.md) — Guo, Wang, Guo, Li, Song,
  Tan, Liu, Bian & Yang, ICLR 2024. The cleanest "LLM as classical-EA
  operator" reference: instantiates Genetic Algorithm and
  Differential Evolution variants with explicit selection rules and
  natural-language templates, evaluated across 31 datasets on two
  distinct underlying LLMs.

## Related wiki pages

- [llm-driven-evolution](llm-driven-evolution.md) — the
  variation-operator concept that the LLM-as-mutator subpattern of
  evolutionary prompt optimization specialises to prompts; the same
  concept covers program-evolution systems (LMX, AlphaEvolve,
  ShinkaEvolve) where the artifact is source code rather than a
  prompt.
- [novelty-and-quality-diversity](novelty-and-quality-diversity.md) —
  the diversity-maintenance machinery Promptbreeder borrows from
  (BERT-cosine-similarity filter), conceptually adjacent.
- [parallel-and-distributed-ga](parallel-and-distributed-ga.md) — the
  classical discrete-EA family that EPPO draws Discrete (1+1)-ES,
  Portfolio, and Lengler from; the same family that powered island-
  model GAs three decades earlier.
- [../foundations/evolutionary-computation.md](../foundations/evolutionary-computation.md) —
  the canonical EC loop these prompt-evolution systems instantiate.
- [../foundations/genetic-algorithms.md](../foundations/genetic-algorithms.md) —
  selection / crossover / mutation as the substrate; both subpatterns
  modify only the variation operator while leaving the rest of the GA
  scaffold intact.
