# Open-Ended Evolution

Open-Ended Evolution (OEE) is the goal of an algorithmic process that
keeps producing new, interesting artifacts indefinitely, rather than
converging to a fixed solution.

## Intuition

Most optimization algorithms — including most
[Genetic Algorithms (GAs)](../foundations/genetic-algorithms.md) — are
designed to *converge*. They start with a population, find the best individual,
and stop. Open-ended evolution flips the goal: success is measured by
the algorithm's ability to keep generating qualitatively new things,
forever, without an external operator restarting it.

The motivating analogy is biological evolution itself, which has been
running for ~4 billion years on Earth and shows no sign of plateauing
in the diversity or complexity of its outputs. The puzzle for the EC
community has been: what is the minimum algorithmic ingredient list
that reproduces this behavior in a computer? Standish (2003) and
several follow-ups have proposed formal definitions; the practical
state of the art is still that we can engineer *episodes* of open-ended
behavior in toy domains but cannot sustain them at scale.

## Mechanics

There is no canonical OEE algorithm; instead, there is a family of
conditions that practitioners try to satisfy. Distilling Soros &
Stanley (2014) and the Brant & Stanley line of work:

- **Non-trivial minimal criterion.** Reproduction must require some
  nontrivial achievement, not just existence; see
  [minimal-criterion.md](minimal-criterion.md).
- **Coevolutionary or environmental coupling.** The pressure on
  population A must change as population B (or the environment) changes,
  so the difficulty bar moves with the population's level. See
  [coevolution.md](coevolution.md).
- **No global convergence pressure.** Selection should not collapse the
  population onto a single optimum.
  [Novelty and quality diversity](novelty-and-quality-diversity.md)
  methods provide one way; the MCC queue-based selection without
  fitness ranking provides another.
- **Unbounded representation.** Both the genome and the artifacts it
  encodes should be able to grow in complexity, not capped at a fixed
  size. The MCC paper explicitly notes that its 50-wall maze cap is an
  artificial constraint imposed for tractability.

Operationally, an OEE-style algorithm tends to look like:

```
while True:                                       # no termination
    pop_A, pop_B = step_coevolution(pop_A, pop_B)
    new_artifacts = collect_all_new(pop_A, pop_B) # do not discard
    log_diversity(new_artifacts)
```

Note the absence of any "best individual" or "convergence" check.

## Why it works (or fails)

The intellectual story (from Lehman & Stanley, "Abandoning Objectives",
2011) is that explicit objective functions actively obstruct
open-endedness when the path to the objective requires going through
intermediate states that look like *failure* under the objective.
Removing or weakening the objective lets the search wander through
those stepping stones. The
[novelty search](novelty-and-quality-diversity.md) line takes this
literally — replace the fitness with a novelty measure. The
[minimal criterion](minimal-criterion.md) line argues that a binary
viability check plus coevolution provides the same benefit without
needing a behavior characterization.

The honest assessment is that OEE works in artificial life worlds
(Tierra, Avida, Chromaria) and in narrow algorithmic toys (MCC mazes,
POET BipedalWalker terrains) but has not yet been demonstrated to scale
to domains practitioners care about. The recent
[LLM-driven evolution](llm-driven-evolution.md) thread — particularly
[ShinkaEvolve](../papers/shinkaevolve.md), whose subtitle is "Towards
Open-Ended And Sample-Efficient Program Evolution" — is an attempt to
reopen this question with a much more powerful variation operator
than NEAT mutation.

## Trade-offs and failure modes

- **Computational cost.** "Run forever" is an expensive ask; everything
  is throwaway compute past the point you stop watching.
- **Hard to evaluate.** OEE algorithms produce a stream of artifacts, not
  a single answer. There are no agreed-upon metrics for "how
  open-ended is this run?", though MAP-Elites' coverage metric and
  diversity measurements (see MCC Section 5) are common proxies.
- **Domain ceiling.** Every published OEE result hits the ceiling of
  its domain (max maze complexity in MCC, max terrain difficulty in
  POET). Whether this is fundamental or an artefact of toy domains is
  the central open question.
- **No convergence guarantee.** By design, you cannot say "the algorithm
  has converged to the right answer". This is fine for exploration,
  uncomfortable for engineering.

## Design choices in the literature

- **Alife worlds.** Tierra (Ray 1991), Avida (Ofria & Wilke 2004),
  Chromaria (Soros & Stanley 2014) — embed the search inside a richer
  environment with implicit interactions.
- **Quality diversity.** [Novelty search](novelty-and-quality-diversity.md),
  NSLC, MAP-Elites — explicit divergence pressure with optional
  quality term.
- **Minimal criterion coevolution.** [MCC](../papers/mcc.md) — drift
  plus binary MC plus coevolutionary coupling; no behavior
  characterization, no archive.
- **Adversarial environment generation.** POET and successors — keep an
  archive of (agent, environment) pairs and let it grow; transfer
  agents across environments.
- **LLM-driven open-ended program search.**
  [ShinkaEvolve](../papers/shinkaevolve.md) — explicit aim of being
  "open-ended" in the program-synthesis setting; uses
  [island populations](parallel-and-distributed-ga.md) to maintain
  divergent discovery streams.

## Open questions

- What is a useful, falsifiable definition of "open-ended"? Standish
  (2003), Bedau (1998), and Banzhaf et al. (2016) all offer candidates;
  none has stuck.
- Can OEE be achieved without a coevolutionary partner — purely through
  novelty pressure on a single population — in a non-toy domain?
- Does the LLM-as-mutation operator actually expand the open-ended
  envelope, or just the speed at which we hit the same domain ceilings?
- Is there a phase transition between "algorithm halts" and "algorithm
  is open-ended", or a continuous spectrum?

## Papers that exemplify this

- [MCC](../papers/mcc.md) — explicit framing as a step toward genuinely
  open-ended algorithms; coevolution + minimal criterion as the recipe.
- [ShinkaEvolve](../papers/shinkaevolve.md) — explicit framing as
  "open-ended" program evolution with an LLM mutation operator.

## Related wiki pages

- [coevolution.md](coevolution.md)
- [minimal-criterion.md](minimal-criterion.md)
- [novelty-and-quality-diversity.md](novelty-and-quality-diversity.md)
- [llm-driven-evolution.md](llm-driven-evolution.md)
- [../foundations/evolutionary-computation.md](../foundations/evolutionary-computation.md)
