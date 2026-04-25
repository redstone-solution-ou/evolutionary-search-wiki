# Concepts

The concept layer is the design-space register of the wiki. Each page
isolates one cross-cutting idea — coevolution, regret, the minimal
criterion, the island model — defines it, gives the mechanics in
pseudocode, and points at the paper leaves that instantiate it.
Concept pages exist so that a reader can walk into the wiki with a
question of the form "what does it mean to do X, and who has done
it?" and get a single page that answers both halves at once.

The ten pages cluster naturally into four small families, each one
modifying a different verb of the canonical loop described in
[foundations](../foundations/foundations.md). Skimming this hub once
makes the rest of the wiki much easier to navigate, because every
paper leaf on this site is a combination of choices from these
families.

## What gets selected

A textbook Genetic Algorithm (GA) selects in proportion to a scalar
fitness function. The first family of concepts is what to do when
that scalar is the wrong primitive.

[Novelty and quality diversity](novelty-and-quality-diversity.md) is
Lehman & Stanley's 2011 alternative: replace fitness with a measure of
how *behaviorally different* each candidate is from everything seen
before. The Multi-dimensional Archive of Phenotypic Elites
(MAP-Elites; Mouret & Clune 2015) is the dominant variant — a grid
that stores the best individual found per behavioural niche, producing
a *repertoire* of solutions instead of a single optimum.

[Minimal criterion](minimal-criterion.md) goes further still. Drop
ranking entirely and keep only a binary "viable / not viable" filter:
a candidate that clears the bar joins the reproductive pool, and any
viable candidate is as eligible as any other. The combination of a
minimal criterion with a coevolving partner population is the engine
of [MCC](../papers/mcc.md).

[Open-ended evolution](open-ended-evolution.md) is the long-run
*goal* this family points toward: an algorithmic process that keeps
producing qualitatively new artifacts indefinitely, rather than
converging to a fixed solution. None of these tricks alone delivers
open-endedness on its own, but novelty + viability + coevolution come
closer than fitness optimisation does.

## How populations are coupled

The second family is what happens when you let candidates' fitness
depend on each other.

[Coevolution](coevolution.md) is the umbrella: any setting where an
individual's fitness depends on other simultaneously evolving
individuals — predator and prey, attacker and defender, sorter and
test case, agent and environment. The classical taxonomy splits this
into competitive and cooperative coevolution, but the two seed papers
in this wiki — [MCC](../papers/mcc.md) and [PAIRED](../papers/paired.md)
— sit interestingly outside that dichotomy.

[Unsupervised environment design (UED)](unsupervised-environment-design.md)
is the deep-Reinforcement-Learning (RL) framing of the same idea:
instead of training an RL agent on a fixed environment, train an
"environment generator" alongside it whose job is to produce the
training environments themselves. PAIRED is the canonical
formalisation; [Prioritized Level Replay (PLR)](../papers/plr.md) is
the practical alternative that drops the learned generator in favour
of replaying past environments by priority.

[Regret as objective](regret-as-objective.md) is what makes UED
*work*. A naive minimax adversary is incentivised to produce
unsolvable environments (impossible mazes); a maximin agent gets
trapped on easy ones. Regret — the gap between the trained agent and
a second "antagonist" agent on the same environment — zeroes out on
both unsolvable environments (both fail equally) and already-mastered
ones (both succeed equally), leaving only the *learnable frontier* as
profitable for the adversary. This is Savage's 1951 minimax-regret
decision rule recast as an RL training signal.

[Automatic curriculum](automatic-curriculum.md) is the downstream
effect across both branches: a difficulty schedule that emerges from
the coupled dynamics, not one that an engineer wrote down. PAIRED's
adversary, MCC's coevolving mazes, PLR's prioritised buffer, and
[AlphaZero](../papers/alphazero.md)'s self-play are four different
mechanisms producing the same end behaviour — training tasks that
track the learner's current skill.

## How variation is performed

The third family rewrites the *operator* that turns parents into
children.

The classical pre-deep-learning move is to constrain mutation to a
formal grammar — Backus-Naur Form, JSON Schema, anything generative —
so every offspring is syntactically valid by construction. That is
[Grammatical Evolution](../papers/grammatical-evolution.md), and is
the canonical non-LLM way to evolve programs or structured
configurations.

The modern move is to delegate variation to a Large Language Model
(LLM): show it parent programs, ask it to produce children, and trust
the LLM's learned prior over "what plausible code looks like" to do
the work that random mutation could not. This is the
[LLM-driven evolution](llm-driven-evolution.md) thread —
foundationally [LMX](../papers/lmx.md) (Meyerson et al. 2023) for the
"LLM-as-crossover" claim, then [AlphaEvolve](../papers/alphaevolve.md)
and [ShinkaEvolve](../papers/shinkaevolve.md) for scaled
program-evolution systems.

[CMA-ES](cma-es.md) — Covariance Matrix Adaptation Evolution Strategy
— is the orthogonal continuous-domain variation operator: a
multivariate-Gaussian sampler whose mean, step size, and full
covariance are *adapted* from success feedback so the sampling
distribution tracks the shape of the fitness landscape. It is the
strong default in continuous black-box optimisation in moderate
dimensions and the variation operator inside both CMA-ME (CMA-ES on
top of MAP-Elites) and the [Model Merging](../papers/model-merging.md)
work that evolves merging coefficients between pretrained models.

## How the population is structured

The fourth family is structural rather than algorithmic.

[Parallel and distributed GAs](parallel-and-distributed-ga.md) — the
island model, introduced by Tanese in 1989 — split the population
across subpopulations that evolve mostly independently and exchange a
few individuals at controlled intervals. The original motivation was
parallel speedup; the lasting contribution is diversity preservation.
Both AlphaEvolve and ShinkaEvolve reuse the island model in 2025 LLM
program-evolution systems for exactly the same reason it was useful
in 1989: a single population of expensively-evaluated candidates
collapses to one mode unless something keeps the modes apart.

## Where to go next

The concept layer makes most sense after the
[foundations](../foundations/foundations.md) hub, which fixes the
canonical loop these pages all modify. After that, the
[paper leaves](../papers/papers.md) are where each modification is
combined with the others, with the actual numbers, ablations, and
limitations from the source. The [overview](../overview.md) tells the
same story as a single linear walkthrough; this hub instead lets you
sample the design space one page at a time.

## Pages in this section

- [automatic-curriculum.md](automatic-curriculum.md) — difficulty
  schedules generated by the algorithm rather than the engineer.
- [cma-es.md](cma-es.md) — Covariance Matrix Adaptation Evolution
  Strategy: continuous-domain black-box optimizer; the variation
  operator inside CMA-ME and Model Merging.
- [coevolution.md](coevolution.md) — two (or more) populations whose
  fitness depends on each other.
- [llm-driven-evolution.md](llm-driven-evolution.md) — the EC loop
  with an LLM as the variation operator.
- [minimal-criterion.md](minimal-criterion.md) — a binary, non-ranked
  viability constraint as the entire selection signal.
- [novelty-and-quality-diversity.md](novelty-and-quality-diversity.md) —
  novelty search, NSLC, MAP-Elites; behavioural divergence as the
  search signal.
- [open-ended-evolution.md](open-ended-evolution.md) — the goal of an
  unbounded stream of interesting artifacts.
- [parallel-and-distributed-ga.md](parallel-and-distributed-ga.md) —
  the island model: subpopulations with periodic migration.
- [regret-as-objective.md](regret-as-objective.md) — Savage's minimax
  regret rule as the adversary's reward in PAIRED.
- [unsupervised-environment-design.md](unsupervised-environment-design.md) —
  the modern deep-RL framing in which the environment-generator is
  itself the thing being optimised.

## Related wiki pages

- [../foundations/foundations.md](../foundations/foundations.md) — the
  canonical loop these concepts all modify.
- [../papers/papers.md](../papers/papers.md) — the paper leaves
  grouped by lineage.
- [../overview.md](../overview.md) — the same story told end to end.
