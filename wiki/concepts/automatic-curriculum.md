# Automatic Curriculum

An automatic curriculum is a difficulty schedule for training that the
algorithm generates from its own state, rather than the engineer
hand-coding a sequence of progressively harder tasks.

## Intuition

A good curriculum exposes the learner to tasks at the edge of its
ability — not so easy that there is nothing to learn, not so hard that
it gets no signal. Vygotsky called this the "zone of proximal
development". Hand-designing such a schedule is one of the most
labor-intensive parts of deploying RL or evolutionary search; it
requires anticipating the learner's trajectory and tuning the
transitions. An *automatic* curriculum lets the training process
discover the schedule by coupling task difficulty to learner skill in
some computable way.

The two seed papers in this wiki produce automatic curricula by very
different means but with the same downstream effect:

- [Protagonist Antagonist Induced Regret Environment Design (PAIRED)](../papers/paired.md)
  trains an environment adversary whose
  [regret](regret-as-objective.md) reward is positive only when the
  protagonist fails on tasks the antagonist can solve — driving the
  adversary toward tasks at the protagonist's frontier.
- [Minimal Criterion Coevolution (MCC)](../papers/mcc.md) coevolves
  agents and mazes under a binary
  [Minimal Criterion (MC)](minimal-criterion.md); as agents get
  better, trivially-solvable mazes drop out of viability and harder
  ones must emerge for the maze population to satisfy its MC.

In both cases, no engineer wrote a difficulty schedule. The schedule
is a side effect of the coupled dynamics.

## Mechanics

Three broad mechanisms appear in the literature:

**Adversarial.** A second learner (or learned generator) produces tasks
whose reward depends on the first learner's performance. Examples:
PAIRED, POET, ACCEL.

```
loop:
    task = adversary.generate(skill_proxy=learner)
    reward = run(task, learner)
    learner.update(reward)
    adversary.update(curriculum_signal(task, reward))
```

**Selection from a pool.** Maintain a population of tasks. Each step,
pick a task to train on according to some priority signal (regret,
value-loss, novelty). Prioritized Level Replay (PLR; Jiang et al.
2021) is the canonical example.

```
pool = task_buffer()
loop:
    task = sample(pool, weight=priority(task, learner))
    reward = run(task, learner)
    learner.update(reward)
    pool.update_priority(task, signal(reward))
```

**Coevolutionary viability.** Two populations evolve under MC-style
constraints; the difficulty curve is implicit in the changing
viability region. MCC is the canonical example.

**Symmetric self-play.** A single policy plays against copies of
itself. As the policy improves, its opponents improve in lockstep,
so the difficulty naturally tracks current skill without any
external schedule. [AlphaZero](../papers/alphazero.md) is the
canonical modern example; this pattern also appears in AlphaStar,
OpenAI Five, and most competitive game-AI systems.

```
loop:
    pop_A.step()
    pop_B.step()
    enforce_MC(pop_A, pop_B)            # difficulty implicit in MC
```

## Why it works

A curriculum is useful when (a) the task space contains a continuum of
difficulties, (b) easier tasks help build skills required by harder
tasks, and (c) the learner's current skill is observable. Automatic
curricula require that the *system* can detect the learner's frontier
without an external evaluator. Regret, novelty, value-loss, and the
binary MC are all candidate frontier-detection signals.

Empirically, automatic curricula outperform fixed difficulty schedules
on procedurally generated environments (PAIRED on MiniGrid mazes; PLR
on Procgen; POET on BipedalWalker terrains). The gap shrinks on
small, well-understood task families where a hand-designed schedule
is plausible.

## Trade-offs and failure modes

- **Frontier mis-estimation.** If the curriculum signal is biased — the
  adversary is weak, the value function is uncalibrated, the MC is too
  loose — the curriculum drifts off the actual frontier. The learner
  trains on the wrong distribution.
- **Catastrophic forgetting.** Aggressive curricula can move the
  training distribution faster than the learner's policy can keep up,
  causing earlier skills to disappear. Mitigations: replay buffers
  spanning the full difficulty range, archives of past tasks, periodic
  re-evaluation on a fixed test suite.
- **Curriculum collapse.** All three mechanisms can collapse to a
  narrow region of the task space (the adversary finds one easy
  exploit, the pool fixates on a few priorities, the MC region shrinks
  to a single niche). Diversity preservation —
  [novelty pressure](novelty-and-quality-diversity.md), speciation, or
  [island populations](parallel-and-distributed-ga.md) — is the
  standard fix.
- **Sample efficiency vs robustness.** Aggressive frontier curricula
  are sample-efficient but produce policies that overfit to "the
  current frontier" rather than the full task distribution. Mixing in
  uniform task sampling helps.

## Design choices in the literature

- **Self-paced learning** (Kumar et al. 2010) — early supervised-
  learning approach: weight examples by predicted difficulty.
- **Teacher-student curriculum** (Matiisen et al. 2019) — explicit
  teacher policy chooses tasks for a student RL agent.
- **Paired Open-Ended Trailblazer (POET)** (Wang et al. 2019) —
  population of (agent, environment) pairs; environments mutate,
  agents transfer; difficulty thresholds are hand-tuned but the
  schedule emerges from the population dynamics.
- **PAIRED** ([papers/paired.md](../papers/paired.md)) — regret-driven
  adversary; difficulty schedule fully implicit.
- **[Prioritized Level Replay](../papers/plr.md)** (Jiang et al.
  2021) — prioritized replay of high-regret
  past levels; cheaper than PAIRED, often as good or better.
- **Adversarially Compounding Complexity by Editing Levels (ACCEL)**
  (Parker-Holder et al. 2022) — PLR plus mutation-based level
  editing; a hybrid of evolutionary and replay-based curricula.
- **MCC** ([papers/mcc.md](../papers/mcc.md)) — fully implicit
  curriculum from coevolutionary viability under a minimal criterion;
  no explicit difficulty signal.

## Open questions

- Is there a unified theory of "curriculum signals"? Regret, novelty,
  value-loss, MC violations, and TD-error are all used as proxies for
  the same notion of "frontier".
- How robust are automatic curricula to changes in the underlying RL
  algorithm? Most published results use PPO; off-policy methods may
  interact differently.
- Can the curriculum *itself* be an output of interest, not just a
  means to a trained policy? MCC explicitly produces a portfolio of
  evolved tasks as an artifact.
- How do automatic curricula interact with LLM-driven program
  evolution? AlphaEvolve and ShinkaEvolve do not yet have an explicit
  curriculum; whether one would help is open.

## Papers that exemplify this

- [PAIRED](../papers/paired.md) — adversarial automatic curriculum
  driven by regret.
- [MCC](../papers/mcc.md) — coevolutionary automatic curriculum driven
  by mutual minimal-criterion satisfaction.

## Related wiki pages

- [unsupervised-environment-design.md](unsupervised-environment-design.md)
- [regret-as-objective.md](regret-as-objective.md)
- [coevolution.md](coevolution.md)
- [open-ended-evolution.md](open-ended-evolution.md)
- [minimal-criterion.md](minimal-criterion.md)
- [../foundations/evolutionary-computation.md](../foundations/evolutionary-computation.md)
