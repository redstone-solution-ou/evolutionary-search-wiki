# AlphaEvolve: A Coding Agent for Scientific and Algorithmic Discovery

> **Short name:** `alphaevolve` · **arXiv:** [2506.13131](https://arxiv.org/abs/2506.13131) · **PDF:** [local](../../papers/alphaevolve_2506.13131.pdf) · **Date:** 2025-06 · **Venue:** White paper (Google DeepMind)

**Authors:** Alexander Novikov, Ngân Vũ, Marvin Eisenberger, Emilien Dupont, Po-Sen Huang, Adam Zsolt Wagner, Sergey Shirobokov, Borislav Kozlovskii et al. (Google DeepMind)

## Abstract
AlphaEvolve is an evolutionary coding agent that orchestrates an
autonomous pipeline of Large Language Models (LLMs) to improve
algorithms by directly editing code. The system maintains a program database, samples parent programs
plus inspirations, prompts an LLM ensemble to propose code diffs, and
filters candidates through automatic evaluators. Applied to Google's
internal compute stack, AlphaEvolve discovered improvements to data-
center scheduling, hardware accelerator circuits, transformer attention
runtime, and the matrix-multiplication kernels used to train LLMs.
Applied to open mathematical and algorithmic problems, it found a
4×4 complex matrix multiplication scheme using 48 scalar
multiplications — the first improvement in 56 years over Strassen's
1969 algorithm in that setting — and improved state-of-the-art
constructions on ~20% of more than 50 open problems including the
Erdős Minimum Overlap Problem and 11-dimensional Kissing Numbers.

## Key contributions
- A scaled-up successor to FunSearch (Romera-Paredes et al. 2024) that
  evolves *entire code files* of hundreds of lines in any language,
  rather than single Python functions of 10–20 lines.
- An autonomous pipeline coupling four components: a *program
  database* (an evolving archive), a *prompt sampler* that constructs
  rich prompts from past trials, an LLM ensemble (Gemini 2.0 Flash
  + Gemini 2.0 Pro), and a pool of *evaluators* that grade candidates
  on user-specified metrics.
- Demonstrates that LLM-driven evolution can produce *novel
  algorithmic results* — not just hyperparameter tuning — including
  the Strassen improvement, several new mathematical constructions,
  and infrastructure-level wins inside Google.
- Establishes the SEARCH/REPLACE diff format (later adopted by
  [ShinkaEvolve](./shinkaevolve.md)) as the practical mutation
  primitive for LLM-driven program evolution.
- Documents the value of multi-metric optimization, hours-long
  parallel evaluators, and rich prompt context — all departures from
  FunSearch's minimalist setup that, taken together, account for
  AlphaEvolve's jump in State-Of-The-Art (SOTA) capability.

## Method at a glance
The user supplies an initial program, an `evaluate` function, and
optional configuration. AlphaEvolve runs a distributed controller
loop: sample a parent and inspirations from the program database,
construct a prompt, call the LLM ensemble to generate a code diff,
apply the diff to produce a child, run the evaluator, and register the
result. Evolve-block markers (`# EVOLVE-BLOCK-START` / `# EVOLVE-BLOCK-END`)
let the user mark which parts of the code are mutable. The program
database uses [island populations](../concepts/parallel-and-distributed-ga.md)
inherited from FunSearch to maintain diversity.

## Why it matters
AlphaEvolve crystallized the LLM-driven-evolution recipe at production
scale. Its successes — particularly the Strassen-improvement result on
4×4 complex matrices, an open problem that withstood 56 years of human
attention — provide the strongest existing evidence that this style of
search can produce *qualitatively new algorithms* rather than just
optimizing within a known design space. It also establishes the
"works inside a real engineering org" capability that earlier program-
evolution work lacked.

## Strengths
- The Strassen-improvement result on 4×4 complex matrix multiplication
  (48 scalar multiplications) is a *verifiable* algorithmic
  improvement on an open problem, not a benchmark score; this is rare
  evidence that LLM-driven evolution can extend the algorithmic
  frontier.
- The infrastructure-level wins (data-center scheduling, attention
  runtime, MatMul kernels for LLM training) demonstrate the same
  pipeline applies to operational engineering, not just to math
  problems with closed-form scoring.
- The four-component architecture (program database, prompt sampler,
  LLM ensemble, evaluator pool) is a clean separation of concerns and
  has become the de facto template for the broader LLM-evolution
  literature.
- Multi-metric optimization is supported natively (Section 2.1) — the
  evaluator returns a dictionary of scalars — and the prompt sampler
  can attend to all of them, which matters in real engineering
  problems where Pareto trade-offs are common.

## Limitations and open critiques
- Closed-source. The code, prompt templates, and exact LLM
  configurations are not released; community re-implementations
  (OpenEvolve, [ShinkaEvolve](./shinkaevolve.md)) had to reconstruct
  the system from the white paper.
- Sample-inefficient. The white paper reports that "thousands of LLM
  samples suffice" (vs FunSearch's millions), which is still expensive
  in absolute terms when each sample is a Gemini 2.0 Pro call plus a
  long evaluator run. ShinkaEvolve's contribution is largely about
  pushing this further.
- Works only on machine-gradeable problems. The white paper is
  explicit (Section 1) that tasks requiring manual experimentation
  are out of scope. This excludes large parts of scientific discovery
  (anything requiring physical experiments) but happens to include
  most of mathematics, algorithm design, and systems optimization.
- Few ablations. Most components (island populations, prompt format,
  ensemble composition, novelty pressure) are present but their
  individual contributions are not separately measured. Whether the
  improvement over FunSearch is mostly the bigger LLMs or mostly the
  surrounding engineering is not cleanly answered.
- Reproducibility of *runs*. Even with the same initial program and
  the same configuration, exact run reproduction depends on LLM
  determinism settings the white paper does not enumerate.

## Follow-up work and dialogue
AlphaEvolve sits in the middle of a fast-moving line of work. The
direct precursor is FunSearch (Romera-Paredes et al. 2024) — single-
function evolution with a small LLM and millions of samples. The most
significant successor is [ShinkaEvolve](./shinkaevolve.md) (Sakana AI,
2025), which positions itself as the open-source, sample-efficient
counterpart and demonstrates state-of-the-art results on circle packing
in 150 samples (vs AlphaEvolve's "thousands"). Other successors include
OpenEvolve (Sharma 2025) — an open-source AlphaEvolve replication —
and CodeEvolve (Liu et al. 2025). Conceptually, AlphaEvolve also fits
into the [open-ended evolution](../concepts/open-ended-evolution.md)
agenda: the program database is, in spirit, the kind of unbounded-
artifact archive that [MCC](./mcc.md)'s neuroevolution-era predecessors
aimed at, now powered by an LLM mutation operator.

## Reproducibility
- **Open code:** no — only the *results* repository
  (`github.com/google-deepmind/alphaevolve_results`) is public; the
  framework itself is closed.
- **Domains used:** matrix multiplication algorithms (14 sizes
  including 4×4 complex), 50+ open mathematical problems
  (Minimum Overlap, Kissing Numbers, etc.), and four Google
  engineering domains (cluster scheduling, matrix-multiplication
  (MatMul) kernels, Tensor Processing Unit (TPU) arithmetic circuits,
  transformer attention runtime).
- **Compute disclosed:** "thousands of LLM samples suffice"; per-task
  compute including evaluator hours is not enumerated. Evaluators can
  run for hours in parallel on accelerators (Section 1, Table 1).
- **Hyperparameters:** LLM ensemble = Gemini 2.0 Flash + Gemini 2.0
  Pro; the SEARCH/REPLACE diff format is the variation operator;
  exact prompt templates are not published.

## When to cite this paper
Cite AlphaEvolve as the canonical reference for *scaled* LLM-driven
program evolution — full files, multi-metric, hours-long evaluators,
SOTA LLMs — and for the Strassen-on-4×4-complex result. For the broader
LLM-evolution recipe at lower cost and open-source, cite
[ShinkaEvolve](./shinkaevolve.md). For the historical predecessor that
established the basic pattern, cite FunSearch (Romera-Paredes et al.
2024).

## In the knowledge graph
- **Related concepts:** [LLM-driven evolution](../concepts/llm-driven-evolution.md),
  [parallel and distributed GA](../concepts/parallel-and-distributed-ga.md)
  (uses island populations from FunSearch),
  [open-ended evolution](../concepts/open-ended-evolution.md)
- **Foundations:** [genetic algorithms](../foundations/genetic-algorithms.md),
  [evolutionary computation](../foundations/evolutionary-computation.md)
- **See also:** [ShinkaEvolve](./shinkaevolve.md) (open-source,
  sample-efficient counterpart), [Island GA](./island-ga.md)
  (the population-structure ancestor)
