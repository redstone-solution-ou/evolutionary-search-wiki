# Evolutionary Search

This wiki is about a single idea, used in many forms: keep a population
of candidate solutions, perturb them, score them, and let the better
ones produce the next generation. That loop — population, variation,
selection, evaluation — is the common skeleton of every paper indexed
here, from a 1989 distributed Genetic Algorithm running on a hypercube
to a 2025 program-evolution system that asks a Large Language Model
to mutate source code. The interesting story is not the loop itself
but how each lineage *modifies* it, and what new behaviours those
modifications make possible: arms races between coevolving populations,
training curricula that the algorithm writes for itself, "open-ended"
runs that keep producing genuinely new artifacts indefinitely, and
the recent thread of programs evolved by repeatedly editing their own
source.

The rest of this page walks through that story end to end. The wiki
is organized around it: a small set of [foundations](foundations/foundations.md)
covers the canonical loop, a layer of cross-cutting
[concepts](concepts/concepts.md) names each modification of it, and the
[paper leaves](papers/papers.md) record who did what, when. You can
read this page top to bottom without following any link and come away
with the shape of the field; clicking a link takes you to a denser
treatment of the same idea, and clicking a paper takes you to the
primary source.

## The canonical loop

A textbook [Genetic Algorithm (GA)](foundations/genetic-algorithms.md)
keeps a population of `N` genomes — bitstrings, real-valued vectors,
neural-network weights, or whatever the problem calls for. Each
generation it scores every individual by a *fitness* function, picks
parents in proportion to fitness, recombines them by *crossover*,
perturbs them by *mutation*, and replaces some fraction of the
population with the resulting children. This is the entire algorithm.
Everything in the wiki is a deliberate edit to one of those four
verbs.

```
population = random_population(N)
while not converged:
    fitnesses = [evaluate(x) for x in population]
    parents   = selection(population, fitnesses)
    children  = [mutate(crossover(p1, p2)) for p1, p2 in pairs(parents)]
    population = replacement(population, children)
```

This loop is older than deep learning by decades. It is also remarkably
robust: with no model of the fitness landscape, no gradient, and only
the ability to *probe* the objective, it can find competitive solutions
on combinatorial problems, design neural-network topologies, and
discover useful programs. Its weaknesses — premature convergence,
hand-tuned hyperparameters, brittle behaviour on landscapes with
deceptive local optima — are exactly the weaknesses every modern
descendant tries to repair.

The wiki tracks four families of repair, each of which we sketch
below. They are not exclusive: the modern systems (AlphaEvolve,
ShinkaEvolve, ACCEL) typically combine two or three at once.

## Lineage 1 — Distribute the population

If one population converges too quickly, run several at once. The
[island model](concepts/parallel-and-distributed-ga.md), introduced by
Reiko Tanese in 1989 and revisited in the leaf for this wiki by
[Belding (1995)](papers/island-ga.md), splits the population into
subpopulations that evolve mostly independently and exchange a small
number of individuals at controlled intervals. The biological analogy
is Sewall Wright's "shifting balance" theory in evolutionary genetics:
small, semi-isolated demes drift independently and occasionally trade
material when one finds something useful. The algorithmic effect is
both a near-linear parallel speedup (one island per processor) and
better diversity preservation than a single panmictic population of
the same total size.

Tanese's island model would be a historical curiosity if it had not
turned out to be the right substrate for several modern systems. Both
of the LLM-driven program-evolution leaves below — AlphaEvolve and
ShinkaEvolve — run their search inside island subpopulations; in
ShinkaEvolve, Tanese 1989 is cited directly. The 1989 trick reappears
in 2025 because the same problem reappears: a population of expensively
evaluated candidates collapses to one mode unless something keeps the
modes apart.

## Lineage 2 — Drop or weaken the fixed objective

Plain GAs select in proportion to a single fitness number. On hard
landscapes — particularly those where the path to a good solution
runs through intermediate states that *look bad* under the fitness
function — this convergence pressure obstructs progress more than it
helps. Lehman and Stanley's 2011 manifesto *"Abandoning Objectives:
Evolution Through the Search for Novelty Alone"* made this argument
concrete: replace fitness with a measure of how *behaviorally
different* each candidate is from everything seen before, and harder
problems become tractable. This launched the
[novelty search and quality diversity (QD)](concepts/novelty-and-quality-diversity.md)
line of work, of which MAP-Elites (Mouret & Clune 2015) — a grid that
stores the best solution found per behavioural niche — is now the
dominant variant.

The next move is to drop fitness ranking entirely and keep only a
binary "viable / not viable" filter — a [minimal criterion](concepts/minimal-criterion.md).
A candidate either clears the bar and joins the reproductive pool, or
does not, and any viable candidate is as eligible as any other. The
canonical paper here is [Minimal Criterion Coevolution (MCC)](papers/mcc.md),
Brant & Stanley 2017, which coevolves NEAT-evolved maze navigators
against procedurally evolved mazes under exactly this rule —
NEAT itself, NeuroEvolution of Augmenting Topologies (Stanley &
Miikkulainen 2002), is the dominant neural-network-evolving algorithm
that grows networks from minimal structure. The MCC result is a maze
population and a navigator population that get more complex together
without anyone ever defining "good".

The methodological pay-off is that the coupled MCC dynamic produces
[open-ended evolution](concepts/open-ended-evolution.md) — a stream of
qualitatively new artifacts that does not converge to a single answer
— without an explicit difficulty schedule. The same mazes that current
navigators cannot solve become accessible as the navigators improve,
and new mazes evolve to push past the new frontier. This is one half
of the [automatic curriculum](concepts/automatic-curriculum.md) story.

## Lineage 3 — Couple two populations against each other

The other half is the deep-RL framing of the same idea.
[Coevolution](concepts/coevolution.md) is the general name for any
setting in which an individual's fitness depends on other simultaneously
evolving individuals — predator and prey, attacker and defender, sorter
and test case. In the 2010s the deep Reinforcement Learning (RL)
community rediscovered this pattern under a new name and framing:
[Unsupervised Environment Design (UED)](concepts/unsupervised-environment-design.md),
formalised by [PAIRED](papers/paired.md) (Dennis et al., NeurIPS 2020).
Instead of training an agent on a fixed environment, train an
"environment adversary" alongside it whose job is to produce the
training environments themselves. The agent is the prey; the
environment generator is the predator.

PAIRED's central trick is to specify what the adversary *wants* in a
way that produces useful environments. A naive minimax adversary —
"make the agent fail" — is incentivised to produce *unsolvable*
environments (impossible mazes), which provide no learning signal.
PAIRED instead makes the adversary maximise *regret*: the gap between
the trained agent (the "protagonist") and a second trained agent (the
"antagonist") on the same environment. Unsolvable environments have
zero regret because both agents fail equally. Already-mastered
environments also have zero regret because both succeed. The only
profitable region for the adversary is *solvable but not yet solved* —
exactly the learnable frontier. The decision-theoretic name for this
is [minimax regret](concepts/regret-as-objective.md), due to Savage
1951; PAIRED's contribution is to put it inside the modern RL loop.

A practical alternative drops the learned adversary and instead
maintains a buffer of past environments, replaying high-regret ones
preferentially. This is [Prioritized Level Replay (PLR)](papers/plr.md)
(Jiang et al., ICML 2021); it is cheaper than PAIRED, often more
sample-efficient, and now the dominant baseline in this lineage. ACCEL
(Adversarially Compounding Complexity by Editing Levels;
Parker-Holder et al. 2022) extends PLR by mutating past environments
in addition to replaying them, closing the loop with the evolutionary
side of the family.

A particularly clean special case of coevolution is *self-play*: one
population plays against copies of itself, which automatically tracks
the current skill level. [AlphaZero](papers/alphazero.md) (Silver et
al., DeepMind 2017/2018) is the canonical reference — a single neural
network that masters chess, shogi, and Go from random play, with the
self-play dynamic providing the entire training curriculum. The
mechanism that produces an automatic curriculum is the same as in MCC
or PAIRED, just with one population instead of two.

## Lineage 4 — Replace the variation operator

The fourth modification leaves selection alone but rewrites how
children are produced.

The classical move, predating modern neural networks, is to constrain
mutation to a *grammar*. [Grammatical Evolution](papers/grammatical-evolution.md)
(O'Neill & Ryan, IEEE TEC 2001) keeps the genome as a simple integer
string but interprets it through a user-supplied formal grammar — Backus-Naur
Form (BNF), JSON Schema, or any other generative grammar. Every
offspring is therefore syntactically valid by construction: the
grammar guarantees what random mutation cannot. This is the canonical
non-LLM way to evolve programs, configurations, or any structured
text.

The modern move, made possible by Large Language Models (LLMs),
is to delegate variation to a learned model of "what plausible code
looks like". [Language Model Crossover (LMX)](papers/lmx.md) (Meyerson
et al., 2023) is the foundational paper: prompt a pretrained LLM with
several parent examples and treat its output as evolutionary
crossover. The empirical observation is that the LLM's output, by
construction, lies in a kind of weighted average of the parents' style
and content — exactly the inductive bias crossover is supposed to
enforce. From there the recipe scales:
[AlphaEvolve](papers/alphaevolve.md) (Novikov et al., DeepMind 2025)
applies it to full-file program evolution, with state-of-the-art LLMs
and hours-long evaluators, and finds a 4×4 complex matrix
multiplication algorithm that improves on Strassen's 1969 bound (the
first sub-cubic matrix-multiplication method) in that setting.
[ShinkaEvolve](papers/shinkaevolve.md) (Lange et al., Sakana AI 2025)
is the open-source counterpart focused on sample efficiency: it adds
power-law parent sampling, code-embedding novelty rejection, and a
bandit that learns which LLM to use when. Both reuse the island model
from Lineage 1 to keep their LLM-generated programs from collapsing
to a single mode. This is the central thread of
[LLM-driven evolution](concepts/llm-driven-evolution.md).

A different way to "evolve over neural networks" is to mutate the
weights themselves rather than the source code that produces them.
[Model Merging](papers/model-merging.md) (Akiba et al., Sakana AI
2024) treats the parameters of multiple pretrained models as the
genome and uses [Covariance Matrix Adaptation Evolution Strategy
(CMA-ES)](concepts/cma-es.md) — the canonical continuous-domain
evolution strategy, which adapts a multivariate Gaussian sampling
distribution from success feedback — to search the
continuous merging coefficients. Reproduction is arithmetic and
thousands of times cheaper than training a model from scratch; the
result is a Japanese-language math model produced by merging an
English math model with a general Japanese model, without any
gradient descent in between.

## Why these lineages belong together

The four lineages look different on the surface — distributed
combinatorial GAs, neuroevolution of mazes, deep-RL curricula,
LLM-prompted program edits — but they answer the same family of
questions. *Where does the variation come from?* (random bit-flip,
NEAT structural mutation, RL environment generator, LLM completion).
*Where does the selection signal come from?* (scalar fitness,
behavioural novelty, binary viability, regret, win-rate). *How is
diversity preserved?* (islands, novelty archive, MAP-Elites grid,
speciation, code-embedding novelty rejection). *How does difficulty
escalate?* (engineer-tuned curriculum, coevolutionary partner,
adversary's regret reward, self-play). Each paper picks one answer
per question; the wiki's concept pages collect all the answers to a
single question across papers, so the design space becomes legible.

This is also why the wiki has the shape it has. The
[foundations](foundations/foundations.md) layer — two pages — covers
the canonical loop and the broader Evolutionary Computation (EC)
family, so every later page can assume vocabulary like *selection*,
*fitness*, *population*, *NEAT*, and *evolution strategy* without
redefining it. The [concepts](concepts/concepts.md) layer — ten pages
— is the design-space register: one page per cross-cutting idea
(coevolution, minimal criterion, novelty / QD, regret, automatic
curriculum, UED, island model, LLM-driven evolution, CMA-ES,
open-ended evolution). The [papers](papers/papers.md) layer — ten
leaves at the moment — records who instantiated which combination of
those ideas, with the actual numbers, ablations, and limitations from
the source.

## Where to go next

If you arrived wanting to learn the area from scratch, the
[foundations hub](foundations/foundations.md) is the right next page:
two short reads that establish the canonical loop and the broader EC
family.

If you have a specific question, the [index](index.md) is a flat
catalogue of every wiki page with a one-line gloss. The [log](log.md)
is the chronological record of what has been ingested and rewritten,
useful when something on this page seems out of date.

A few suggested reading paths, depending on your interest:

- **Coevolution lineages compared.** Read
  [coevolution](concepts/coevolution.md) for the shared framing, then
  walk through [MCC](papers/mcc.md) (the neuroevolution branch) and
  [PAIRED](papers/paired.md) (the deep-RL branch), noting how each
  instantiates "second population drives complexity".
- **The LLM-evolution thread.** Read
  [LLM-driven evolution](concepts/llm-driven-evolution.md), then
  [LMX](papers/lmx.md) for the foundational claim, then
  [AlphaEvolve](papers/alphaevolve.md) and
  [ShinkaEvolve](papers/shinkaevolve.md) for the scale-vs-sample-efficiency
  contrast.
- **Automatic curricula across paradigms.** Read
  [automatic curriculum](concepts/automatic-curriculum.md), then any of
  [PAIRED](papers/paired.md), [PLR](papers/plr.md),
  [MCC](papers/mcc.md), or [AlphaZero](papers/alphazero.md) to see four
  different mechanisms producing the same end behaviour.
- **The 1989-island-model thread.** Start at
  [Island GA](papers/island-ga.md), then
  [parallel and distributed GA](concepts/parallel-and-distributed-ga.md)
  for the mechanics, then [AlphaEvolve](papers/alphaevolve.md) and
  [ShinkaEvolve](papers/shinkaevolve.md) to see Tanese 1989 reappearing
  in 2025 LLM systems.
- **Pick a method for a use case.** Curriculum for a deep-RL agent →
  [PAIRED](papers/paired.md) or [PLR](papers/plr.md). Open-ended
  artifact generation without a pre-defined fitness →
  [MCC](papers/mcc.md). Parallelize an existing GA →
  [Island GA](papers/island-ga.md). Use an LLM to evolve a piece of
  code → [AlphaEvolve](papers/alphaevolve.md) for scale,
  [ShinkaEvolve](papers/shinkaevolve.md) for sample efficiency or
  open source. Combine pretrained models without retraining →
  [Model Merging](papers/model-merging.md). Continuous black-box
  optimisation in moderate dimensions →
  [CMA-ES](concepts/cma-es.md).

## Knowledge-graph sketch

```
evolutionary-search-wiki
|
+-- foundations/
|   +-- genetic-algorithms             (selection, crossover, mutation, fitness)
|   +-- evolutionary-computation       (ES, GP, neuroevolution, NEAT, the bridges)
|
+-- concepts/                          (one page per modification of the loop)
|   +-- parallel-and-distributed-ga    ----> Island GA, AlphaEvolve, ShinkaEvolve
|   +-- novelty-and-quality-diversity  ----> MAP-Elites (foundation), MCC, ShinkaEvolve
|   +-- minimal-criterion              ----> MCC
|   +-- open-ended-evolution           ----> MCC, ShinkaEvolve
|   +-- coevolution                    ----> MCC, PAIRED, AlphaZero
|   +-- unsupervised-environment-design ---> PAIRED, PLR
|   +-- regret-as-objective            ----> PAIRED, PLR
|   +-- automatic-curriculum           ----> PAIRED, PLR, MCC, AlphaZero
|   +-- llm-driven-evolution           ----> LMX, AlphaEvolve, ShinkaEvolve
|   +-- cma-es                         ----> Model Merging
|
+-- papers/                            (lineage groupings; see papers/papers.md)
|   +-- island-ga                      (Belding 1995; cites Tanese 1989, Whitley 1998)
|   +-- grammatical-evolution          (O'Neill & Ryan, IEEE TEC 2001)
|   +-- mcc                            (Brant & Stanley, GECCO 2017)
|   +-- alphazero                      (Silver et al., Science 2018)
|   +-- paired                         (Dennis et al., NeurIPS 2020)
|   +-- plr                            (Jiang et al., ICML 2021)
|   +-- lmx                            (Meyerson et al., ACM TELO 2024)
|   +-- model-merging                  (Akiba et al., Sakana AI 2024)
|   +-- alphaevolve                    (Novikov et al., DeepMind 2025)
|   +-- shinkaevolve                   (Lange et al., Sakana AI 2025)
```

## Related wiki pages

- [foundations/foundations.md](foundations/foundations.md) — the
  canonical loop and the broader EC family.
- [concepts/concepts.md](concepts/concepts.md) — the design-space
  register: one page per cross-cutting idea.
- [papers/papers.md](papers/papers.md) — the paper leaves, grouped by
  lineage.
- [index.md](index.md) — flat catalogue, first stop for a known topic.
- [log.md](log.md) — chronological record of ingests and rewrites.
