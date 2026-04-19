# LLM-Driven Evolution

LLM-driven evolution keeps the population, selection, and evaluation
loop of [evolutionary computation](../foundations/evolutionary-computation.md)
but replaces the variation operator (mutation, crossover) with calls to
a large language model that proposes edits or rewrites of program
candidates.

## Intuition

Classical evolutionary search over code suffers from a brutal variation
problem. Random mutation of source code almost always produces broken
programs; even targeted operators (subtree replacement in genetic
programming) produce children that compile but rarely improve.
Crossover between programs is even worse because the semantics of two
parents do not compose along syntactic boundaries.

LLMs change this picture. A modern LLM, given a parent program and a
prompt that frames the change to make, produces a child that almost
always compiles, frequently runs, and often improves on the parent.
The LLM is doing the work that mutation and crossover were always
*supposed* to do — propose plausible variations from a learned prior
over what code looks like — but with vastly better priors than random
syntactic edits.

The wiki's relevant papers — [AlphaEvolve](../papers/alphaevolve.md)
(Google DeepMind, 2025) and
[ShinkaEvolve](../papers/shinkaevolve.md) (Sakana AI, 2025) — both
follow this pattern: keep the EA scaffolding, swap the variation
operator for an LLM call.

## Mechanics

The shared template:

```
program_db = init_archive(seed_program)
while not converged:
    parent, inspirations = sample_parents(program_db)
    prompt = build_prompt(parent, inspirations, task_spec)
    diff = llm.generate(prompt)                      # variation operator
    child = apply_diff(parent, diff)
    if not compiles_or_invalid(child):
        continue                                     # cheap reject
    metrics = evaluate(child)                        # automatic eval
    program_db.add(child, metrics)
```

The non-trivial design choices are:

- **Mutation granularity.** SEARCH/REPLACE diffs (small, targeted),
  full-file rewrites (large, expressive), or crossover-style
  combinations of two parents.
- **Parent sampling strategy.** Uniform, fitness-proportional,
  power-law over rank, or weighted by both performance and novelty
  (offspring count). ShinkaEvolve's Section 3.1 has the most
  detailed treatment.
- **Inspiration set.** The prompt typically contains the parent plus a
  set of "inspiration" programs drawn from the archive (top-K by
  fitness, plus a random sample). This conditions the LLM on what good
  solutions in this problem look like.
- **Novelty filtering.** Embed candidate programs and reject those too
  similar to the archive (ShinkaEvolve's code-novelty rejection
  sampling). Avoids burning evaluations on near-duplicates.
- **LLM ensemble.** Use multiple models and weight them by recent
  success (ShinkaEvolve's bandit-based selection), or use a single
  large model throughout.
- **Population structure.** Both AlphaEvolve and ShinkaEvolve use
  [island populations](parallel-and-distributed-ga.md) to maintain
  diverse solution lineages; islands rarely exchange members.

## Why it works

Three properties of LLMs make this work where classical genetic
programming did not.

**Strong syntactic prior.** LLM outputs are almost always syntactically
valid in the target language. Compile-rate is no longer the bottleneck.

**Semantic plausibility.** LLMs understand idioms, common algorithms,
and library APIs. A mutation prompt like "make this function 10× faster"
produces edits that target the actual cost centers, not random
character flips.

**Recombination via prompting.** The LLM can be shown two parent
programs in the same prompt and asked to combine them. The "crossover"
respects semantics in a way that syntactic crossover cannot.

The empirical payoff in the seed papers is dramatic. AlphaEvolve found
a 4×4 complex matrix multiplication algorithm with 48 scalar
multiplications, beating Strassen's 1969 algorithm in that setting.
ShinkaEvolve found a state-of-the-art circle packing in 150 LLM
samples, where prior LLM-driven approaches needed thousands.

## Trade-offs and failure modes

- **Cost per evaluation.** Each LLM call costs orders of magnitude
  more than a classical mutation. Sample efficiency therefore matters
  much more than in NEAT-era EC; ShinkaEvolve is explicitly optimized
  for this.
- **LLM bias toward training distribution.** The variation operator can
  only propose edits the LLM can imagine. Truly novel algorithms may
  be unreachable if they look unlike anything in the LLM's training
  data.
- **Need for automatic evaluator.** The whole loop requires
  `evaluate(child)` to return a meaningful scalar without human
  intervention. Tasks where evaluation requires manual assessment are
  out of scope (AlphaEvolve Section 1 makes this explicit).
- **Mode collapse.** LLM samples can cluster around a few preferred
  patterns; without active novelty pressure, the search collapses.
  ShinkaEvolve's rejection sampling and AlphaEvolve's island structure
  are both responses to this.
- **Reproducibility.** LLM outputs depend on model version, sampling
  temperature, and inference-time compute settings. Replicating a run
  exactly is hard.

## Design choices in the literature

- **FunSearch** (Romera-Paredes et al. 2024) — the precursor: a single
  function evolved by a small LLM, millions of samples, single metric.
  Established the pattern.
- **Eureka** (Ma et al. 2023) — LLM-driven evolution of reward
  functions for RL.
- **AlphaEvolve** ([papers/alphaevolve.md](../papers/alphaevolve.md)) —
  scaled-up successor to FunSearch: full files instead of single
  functions, SOTA LLMs, hours-long evaluations, parallel infrastructure,
  multiple metrics. Improved Strassen's algorithm and several open
  math problems.
- **ShinkaEvolve** ([papers/shinkaevolve.md](../papers/shinkaevolve.md))
  — open-source reimplementation focused on sample efficiency:
  bandit-based LLM ensemble, novelty rejection sampling, weighted
  parent selection. State-of-the-art results in 100s of samples
  rather than 1000s.
- **OpenEvolve** (Sharma 2025) — open-source AlphaEvolve replication.
- **LLM4AD** (Liu et al. 2024) — LLM-assisted algorithm discovery
  framework.
- **Darwin Gödel Machine** (Zhang et al. 2025) — self-modifying agentic
  system that evolves its own scaffolding, cited as related work in
  ShinkaEvolve.

## Open questions

- Is the LLM the bottleneck, or the evaluator? On problems with cheap
  evaluation (combinatorial optimization, math constructions), the
  evaluator is fast but the LLM is expensive. On problems with
  expensive evaluation (training a model and measuring quality), the
  reverse holds.
- How should population structure be designed when the variation
  operator is itself learned and expensive? Classical island-GA
  parameters (migration rate, subpopulation size) need re-tuning.
- Can LLM-driven evolution escape its training distribution to discover
  algorithms that do not resemble anything humans have written?
  AlphaEvolve's Strassen improvement is suggestive but does not settle
  the question.
- What is the right crossover operator when the LLM can synthesize
  novel combinations of multiple parents? Naive "show both parents,
  ask for a child" works but has not been studied carefully.
- Is there a useful coevolutionary version of LLM-driven evolution —
  two populations of programs evaluating each other?

## Papers that exemplify this

- [AlphaEvolve](../papers/alphaevolve.md) — the canonical reference
  for scaled, multi-metric LLM-driven evolution; demonstrated novel
  algorithmic discoveries.
- [ShinkaEvolve](../papers/shinkaevolve.md) — the sample-efficient,
  open-source counterpart; introduced rejection sampling and
  bandit-based ensemble selection.

## Related wiki pages

- [../foundations/genetic-algorithms.md](../foundations/genetic-algorithms.md)
- [../foundations/evolutionary-computation.md](../foundations/evolutionary-computation.md)
- [parallel-and-distributed-ga.md](parallel-and-distributed-ga.md)
- [novelty-and-quality-diversity.md](novelty-and-quality-diversity.md)
- [open-ended-evolution.md](open-ended-evolution.md)
