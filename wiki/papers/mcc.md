# Minimal Criterion Coevolution: A New Approach to Open-Ended Search

> **Short name:** `mcc` · **DOI:** [10.1145/3071178.3071186](https://doi.org/10.1145/3071178.3071186) · **PDF:** [local](../../papers/mcc_brant-stanley_2017.pdf) · **Date:** 2017-07 · **Venue:** GECCO 2017

**Authors:** Jonathan C. Brant, Kenneth O. Stanley (UCF)

## Abstract
Recent non-objective evolutionary methods promote divergence by
rewarding behavioral novelty, but still differ significantly from
nature's mechanism, which uses a single fundamental constraint —
survive long enough to reproduce. The paper introduces Minimal
Criterion Coevolution (MCC): two coevolving populations, each subject
to its own minimal criterion (MC), without any behavior
characterization or novelty archive. The approach is tested in a novel
maze navigation domain in which mazes and NEAT-evolved navigators
coevolve and increase in complexity simultaneously. A single run
produces a broad range of maze topologies and successful agent
trajectories, suggesting MCC as a step toward genuinely open-ended
algorithms.

## Key contributions
- Introduces Minimal Criterion Coevolution (MCC; see
  [minimal-criterion.md](../concepts/minimal-criterion.md)) as a new
  branch of [coevolution](../concepts/coevolution.md) outside the
  traditional competitive / cooperative dichotomy: the two
  populations are not rewarded based on each other's *success*, only
  on whether the *interaction* satisfies a binary MC.
- Demonstrates the first (per the authors' knowledge) production of
  diverse functional artifacts in a single evolutionary run *without*
  a behavior characterization, novelty archive, or fitness ranking.
- Specifies the queue-based selection mechanism for MC populations:
  individuals are stored in fixed-size FIFO queues; every viable
  individual gets at least one chance to reproduce.
- Proposes and evaluates a *speciated* MCC variant using
  genetics-based speciation (NEAT's built-in mechanism for the agents,
  Cantor-pairing genetic distance for the mazes), and shows that
  speciation is required to maintain divergent functional diversity
  (Section 5.2, p<0.001).
- Frames MCC as a methodological alternative to the
  [novelty / quality-diversity](../concepts/novelty-and-quality-diversity.md)
  lineage, citing the BC-design problem and finite-BC ceiling as
  motivations.

## Method at a glance
Two queues — one of NeuroEvolution of Augmenting Topologies
(NEAT)-evolved maze-navigating Artificial Neural Networks (ANNs)
(capacity 250), one of evolved mazes (capacity 50). The agent
Minimal Criterion (MC): solve at least one maze in the current maze
queue. The maze MC: be solvable by at least
one agent in the current agent queue. Selection is by queue insertion
order; reproduction by NEAT mutation (agents) or wall/passage
mutation plus add-wall complexification (mazes). New offspring satisfying
the MC are enqueued; queue overflow removes the oldest. Bootstrap is
by a novelty-search pre-pass that produces 20 viable seed agents and
10 viable seed mazes.

## Why it matters
MCC is the cleanest demonstration that
[open-ended evolution](../concepts/open-ended-evolution.md) is
achievable outside traditional alife worlds with a startlingly simple
recipe: two coevolving populations, a binary minimal criterion, and
genetics-based speciation. It is the methodological foil to both the
novelty-search lineage (no Behavior Characterization (BC), no
archive) and the fitness-driven Genetic Algorithm (GA) lineage (no
fitness ranking). It is also the canonical reference for
the "MCC pattern" reused in later work, including modern adversarial
environment generation and program-evolution systems.

## Strengths
- The dual-MC design replaces the entire fitness-function design
  problem with a binary check — a strictly smaller engineering
  surface than novelty search's BC choice or QD's grid design.
- Coevolutionary coupling is built *into* the MC itself
  (`satisfies_MC(child_A, queue_B)`), not bolted on as a separate
  interaction step. This unifies the two populations' selection
  pressures conceptually and operationally.
- The queue-based parent selection is a simple and effective
  divergence-preserving mechanism that does not require any explicit
  behavioral archive or novelty score.
- The speciated variant produces qualitatively different runs — a
  visible, reproducible separation between control and treatment in
  Figures 2 and 3 — with a statistically significant difference in
  trajectory diversity (Welch's t-test, p<0.001, Figure 4).

## Limitations and open critiques
- The maze domain caps complexity at the maximum number of interior
  walls supportable by a fixed canvas. The paper explicitly flags this
  as an artificial constraint imposed for tractability and notes that
  evolved agents reach this ceiling well before the time budget runs
  out. Whether MCC produces *unbounded* complexity is therefore not
  tested by this paper.
- The unspeciated control collapses to consistent maze topologies and
  trajectories within a run (Section 5.1), showing that the binary MC
  alone is not sufficient to maintain diversity. Genetics-based
  speciation is doing a non-trivial fraction of the work, even though
  the paper foregrounds the MC.
- Direct quantitative comparison to objective-driven and novelty-based
  algorithms is not feasible (Section 4.3 is explicit about this);
  the empirical evidence is qualitative diversity figures plus
  intra-MCC ablations.
- The bootstrap requires a *novelty search* pre-pass to generate the
  initial viable seed populations. The dependence on novelty search
  for initialization is a small but real concession to the lineage
  the paper is positioning itself against.
- The maze representation is hand-designed (recursive bisection, fixed
  start/target positions). Generalization to richer 2D or 3D worlds is
  speculation, not evidence.

## Follow-up work and dialogue
The direct successor is Brant & Stanley's "Benchmarking open-endedness
in minimal criterion coevolution" (GECCO 2019), which proposes
quantitative open-endedness metrics specifically for the MCC dynamics.
The conceptual lineage flows forward into POET (Wang et al. 2019),
which can be read as MCC reframed for deep RL — though POET keeps
explicit fitness thresholds where MCC drops them — and into
[PAIRED](./paired.md), which replaces MCC's binary MC with regret as
the adversary's signal. Both ShinkaEvolve and OpenAI / Sakana lines of
work cite the open-ended-evolution agenda this paper helped seed; see
[ShinkaEvolve](./shinkaevolve.md) for the modern LLM-era inheritor of
the open-ended-search framing.

## Reproducibility
- **Open code:** yes — released at `bit.ly/2oM22YK` (cited in Section
  4.4); built on top of SharpNEAT v3.0 (Green 2016).
- **Domains used:** a single maze-navigation domain on a 320×320 unit
  square with fixed start (upper-left) and target (lower-right);
  agents have six rangefinder sensors and four pie-slice radar
  sensors, two actuators (Section 4).
- **Compute disclosed:** 20 runs per variant of 2,000 batches each;
  no GPU or wall-clock cost reported.
- **Hyperparameters:** agent queue capacity 250, maze queue capacity
  50, agent connection mutation 0.7 / add-connection 0.1 / add-neuron
  0.01 / delete-connection 0.0001, maze wall-mutation 0.05 /
  passage-mutation 0.05 / add-wall 0.7, agent velocity cap 3
  units/sec, 600 timesteps per trial, novelty-search bootstrap with
  250 agents on 10 random 8-wall mazes (Section 4.4).

## When to cite this paper
Cite MCC as the canonical reference for *minimal-criterion
coevolution* — dual-population coevolution with a binary viability
constraint, no behavior characterization, no novelty archive, no
fitness ranking. It is also the right citation for the dual-queue
selection pattern and for the claim that open-ended evolution can be
produced outside alife worlds with this recipe.

## In the knowledge graph
- **Related concepts:** [coevolution](../concepts/coevolution.md),
  [minimal criterion](../concepts/minimal-criterion.md),
  [open-ended evolution](../concepts/open-ended-evolution.md),
  [novelty and quality diversity](../concepts/novelty-and-quality-diversity.md),
  [automatic curriculum](../concepts/automatic-curriculum.md)
- **Foundations:** [genetic algorithms](../foundations/genetic-algorithms.md),
  [evolutionary computation](../foundations/evolutionary-computation.md)
  (neuroevolution branch via NEAT)
- **See also:** [PAIRED](./paired.md) (the deep-RL counterpart that
  replaces the binary MC with regret)
