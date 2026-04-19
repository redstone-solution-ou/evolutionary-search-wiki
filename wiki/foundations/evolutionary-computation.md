# Evolutionary Computation

Evolutionary Computation (EC) is the umbrella term for population-based
stochastic search algorithms inspired by biological evolution. It
includes Genetic Algorithms (GAs; see
[genetic-algorithms.md](genetic-algorithms.md)) but is broader:
Evolution Strategies (ES), Genetic Programming (GP), neuroevolution,
Estimation-of-Distribution Algorithms (EDAs), and the more recent
hybrids with deep learning and Large Language Models (LLMs).

## Intuition

Every EC method shares the same skeleton: a population of candidates, a
selection mechanism that prefers fitter candidates, and a variation
mechanism that produces new candidates from existing ones. The methods
differ in what the candidates are (bitstrings, real vectors, trees,
neural networks, source code), how variation works (crossover and
mutation, Gaussian perturbation, subtree replacement, structural
mutation, LLM rewrite), and what fitness means (a scalar function, a
distribution-matching metric, a behavioral diversity score, a binary
viability check, performance against another evolving population).

The sub-fields are mostly defined by their representation:

- **Genetic algorithms (GAs)** — fixed-length bitstrings or real vectors;
  see [genetic-algorithms.md](genetic-algorithms.md).
- **Evolution Strategies.** Real-valued vectors with self-adaptive
  Gaussian mutation; the Covariance Matrix Adaptation Evolution
  Strategy ([CMA-ES](../concepts/cma-es.md)) adapts the full
  covariance matrix and is the strong default for continuous
  black-box optimization in moderate dimensions.
- **Genetic Programming.** Variable-size tree-structured programs;
  the variation operator swaps subtrees.
- **Neuroevolution.** Evolving neural-network weights and/or
  topologies; NeuroEvolution of Augmenting Topologies (NEAT; Stanley &
  Miikkulainen 2002) is the canonical method, growing networks from
  minimal structure by adding nodes and connections under historical
  marking.
- **Estimation-of-Distribution Algorithms.** Instead of recombining
  individuals directly, fit a probabilistic model to the population
  and sample from it.

## Where coevolution sits

Plain EC evaluates each individual in isolation against a fixed fitness
function. [Coevolution](../concepts/coevolution.md) breaks this
assumption: an individual's fitness depends on other evolving
individuals, either in the same population (competitive self-play) or
in a second population (predator-prey, agent-environment, learner-test).
Coevolution sits inside the EC family but raises new dynamics — arms
races, mediocre stable states, automatic curricula — that fixed-fitness
EC does not have.

The wiki's two seed papers both belong here:

- [MCC](../papers/mcc.md) is dual-population NEAT-coevolution under a
  binary [minimal criterion](../concepts/minimal-criterion.md).
- [PAIRED](../papers/paired.md) is a deep-RL descendant: the
  "populations" are neural-network policies trained by RL, and the
  fitness signal is [regret](../concepts/regret-as-objective.md).

## The bridge to deep RL

Modern Reinforcement Learning (RL) shares most of its problems with EC:
sample-efficient stochastic search over large parameter spaces with
sparse, noisy reward. The two communities have converged in several
places:

- **Evolution as a baseline for RL.** Salimans et al. (2017) showed that
  ES rivals deep RL on Atari and MuJoCo at sufficient scale; OpenAI's
  ES paper popularized this comparison.
- **Neuroevolution for hard exploration.** Such et al. (2018) "Deep
  Neuroevolution" showed simple GAs can train deep networks competitively
  on Atari, especially on hard-exploration games.
- **Population-Based Training (PBT).** A coevolution-flavored hybrid
  used to tune RL hyperparameters online.
- **Adversarial environment generation.** [PAIRED](../papers/paired.md),
  the Paired Open-Ended Trailblazer (POET), and Adversarially
  Compounding Complexity by Editing Levels (ACCEL) adopt the EC
  pattern of "second population shapes the first" inside an RL
  training loop.

## The bridge to LLMs

The most recent thread:
[LLM-driven evolution](../concepts/llm-driven-evolution.md) keeps the
EC selection-variation-evaluation loop but uses a large language model
as the variation operator over code. The LLM proposes mutations and
recombinations of program candidates; an automatic evaluator scores
them; selection proceeds as usual. [AlphaEvolve](../papers/alphaevolve.md)
and [ShinkaEvolve](../papers/shinkaevolve.md) are the clearest current
examples; both inherit the
[island model](../concepts/parallel-and-distributed-ga.md) from
distributed GA work for diversity preservation.

## Open questions

- When is EC competitive with gradient-based optimization, and when is
  the extra exploration worth the sample-efficiency cost?
- How should one choose between competitive coevolution, cooperative
  coevolution, and minimal-criterion coevolution for a new problem?
- Can the LLM-driven branch produce truly novel algorithms, or is it
  bounded by the LLM's training distribution?
- What is the right way to combine evolutionary outer loops with
  gradient-based inner loops in modern ML pipelines?

## Papers that exemplify this

- [MCC](../papers/mcc.md) — neuroevolution branch (NEAT) plus
  coevolution; an example of EC with a non-objective selection signal.
- [PAIRED](../papers/paired.md) — deep-RL branch; population-style
  adversarial environment generation around RL agents.
- [Island GA](../papers/island-ga.md) — classical EC parallelism via
  subpopulations and migration.
- [AlphaEvolve](../papers/alphaevolve.md),
  [ShinkaEvolve](../papers/shinkaevolve.md) — LLM-driven EC over source
  code.

## Related wiki pages

- [genetic-algorithms.md](genetic-algorithms.md)
- [../concepts/coevolution.md](../concepts/coevolution.md)
- [../concepts/parallel-and-distributed-ga.md](../concepts/parallel-and-distributed-ga.md)
- [../concepts/llm-driven-evolution.md](../concepts/llm-driven-evolution.md)
