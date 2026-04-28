# Papers

The leaf layer of the wiki — one short page per primary research
paper, with a dense summary, key contributions, method at a glance,
critique, reproducibility facts, and a full "knowledge graph" block
linking up to the relevant [concepts](../concepts/concepts.md) and
[foundations](../foundations/foundations.md). Every claim on a leaf
cites a section, table, or figure of the paper itself; for cross-paper
synthesis, see the concept pages above.

The leaves below are grouped into seven lineages — strands of the
literature that share a research question and a methodological move.
The grouping matters as much as the individual leaves: each lineage
exists because some earlier paper noticed a missing piece and
proposed a way to fill it, and reading the leaves in lineage order
makes that genealogy visible.

## Classical parallel evolutionary computation

The oldest lineage in this wiki, and the one with the most surprising
modern reuse. Reiko Tanese's PhD work in the late 1980s introduced
the *Distributed Genetic Algorithm (DGA)*: split the population
across processors, let each subpopulation evolve mostly independently,
and exchange a small fraction of individuals at controlled intervals.
The biological analogy is Sewall Wright's "shifting balance" theory in
evolutionary genetics; the algorithmic effect is both near-linear
parallel speedup and better diversity preservation than a single
population of the same total size.

- [Island GA](island-ga.md) — *"The Distributed Genetic Algorithm
  Revisited"*, Theodore C. Belding, ICGA-95. Belding revisits Tanese
  1989 on the Royal Road benchmark family — synthetic fitness
  functions designed to test the *building-block hypothesis* by
  rewarding the assembly of contiguous schemata — and explores the
  load-bearing migration parameters. The leaf cites Tanese 1989 and
  Whitley/Rana/Heckendorn 1998 as the seminal and theoretical
  references; Belding is included as the leaf because it is openly
  accessible on arXiv. The same island-model machinery reappears in
  the 2025 LLM-driven leaves below; ShinkaEvolve cites Tanese 1989
  directly.

## Grammar- and schema-constrained evolutionary search

A small but practically important lineage: when the candidates have
to satisfy a formal grammar — a programming-language syntax, a JSON
Schema, a Domain-Specific Language (DSL) — random mutation almost
always produces invalid offspring. Constraining variation to a
grammar fixes this by construction.

- [Grammatical Evolution](grammatical-evolution.md) — O'Neill & Ryan,
  IEEE Transactions on Evolutionary Computation 2001. Genotype is a
  variable-length integer string; phenotype is produced by decoding
  the integers through a user-supplied Backus-Naur Form grammar (or
  any other generative grammar). Genetic operators act on the integer
  genome; the grammar guarantees syntactic validity of every
  offspring. This is the canonical non-LLM way to evolve programs,
  configurations, or any structured-text artifact, and it is the
  classical counterpart to the LLM-driven program-evolution lineage
  below.

## Neuroevolutionary open-ended coevolution

The lineage that asks "what is the minimum algorithmic ingredient list
that produces an unbounded stream of qualitatively new artifacts?"
Built on top of the neuroevolution branch of EC — particularly
NeuroEvolution of Augmenting Topologies (NEAT; Stanley & Miikkulainen
2002), which grows neural networks from minimal structure — and on
the [novelty / quality-diversity](../concepts/novelty-and-quality-diversity.md)
work that established behavioural divergence as a usable search
signal.

- [MCC](mcc.md) — *"Minimal Criterion Coevolution: A New Approach to
  Open-Ended Search"*, Brant & Stanley, GECCO 2017. Two NEAT-based
  populations — maze navigators and procedurally evolved mazes —
  coevolve under nothing but a binary
  [minimal criterion](../concepts/minimal-criterion.md): an individual
  reproduces if and only if some interaction with the other population
  satisfies a viability check. No fitness ranking, no novelty archive,
  no behaviour characterisation. Both populations grow more complex
  in lockstep; the resulting [automatic curriculum](../concepts/automatic-curriculum.md)
  is a side effect of mutual viability rather than an explicit
  difficulty schedule. The canonical reference for archive-free
  open-ended coevolution.

## Self-play coevolution

The cleanest and most widely-cited branch of coevolution in the
modern deep-RL era. A single population (one neural network) plays
against copies of itself; the opponents track current skill
automatically, so the difficulty curve writes itself.

- [AlphaZero](alphazero.md) — Silver et al., DeepMind 2017
  preprint / *Science* 2018. A single neural network plus Monte
  Carlo Tree Search, trained entirely from self-play, masters chess,
  shogi, and Go from random play. The MCTS acts as a *policy
  improvement* operator that produces moves stronger than the raw
  network policy; the self-play data trains the network to match
  those moves; the improved network strengthens the next round of
  search. The cleanest known demonstration that the self-play
  dynamic alone produces a viable training curriculum, with no human
  data beyond the game rules and no game-specific engineering.

## Deep-reinforcement-learning adversarial environment design

The lineage that takes the coevolutionary intuition — let a second
population shape the first — and ports it inside a deep-RL training
loop. The "second population" is now an *environment generator* whose
job is to produce the training tasks themselves. The interesting
design question is what the generator should optimise for.

- [PAIRED](paired.md) — *"Emergent Complexity and Zero-shot Transfer
  via Unsupervised Environment Design"*, Dennis et al., NeurIPS 2020.
  Formalises [Unsupervised Environment Design (UED)](../concepts/unsupervised-environment-design.md)
  as a decisions-under-ignorance problem and instantiates it with the
  Protagonist Antagonist Induced Regret Environment Design
  algorithm. An environment adversary maximises *regret* — the gap
  between the trained "protagonist" agent and a second "antagonist"
  agent on the same environment — which by construction zeroes out on
  unsolvable environments and on already-mastered ones, leaving only
  the *learnable frontier* as profitable. The canonical reference for
  UED.
- [Prioritized Level Replay](plr.md) — Jiang, Grefenstette &
  Rocktäschel, ICML 2021. Drops the learned environment adversary
  entirely. Maintains a buffer of past procedurally-generated levels
  and replays each in proportion to a learning-potential signal
  (typically value-loss or temporal-difference error) treated as a
  proxy for regret. Simpler, cheaper, and typically higher-performing
  than PAIRED on procedural-generation benchmarks; now the dominant
  baseline in this lineage. The Adversarially Compounding Complexity
  by Editing Levels (ACCEL; Parker-Holder et al. 2022) extension adds
  evolutionary editing of past levels on top of PLR.

## LLM-driven program evolution

The most recent thread, and currently the most active. Keep the EC
selection-evaluation loop intact, but replace random mutation and
syntactic crossover with calls to a Large Language Model that proposes
plausible edits to source code. The empirical observation that makes
this work is that the LLM's outputs are almost always syntactically
valid and frequently semantically improving — exactly what classical
random mutation could not achieve on programs.

- [LMX — Language Model Crossover](lmx.md) — Meyerson et al., 2023
  preprint / ACM TELO 2024. The foundational paper. Prompt a
  pretrained LLM with several parent examples and treat the LLM's
  output as evolutionary crossover. The behaviour generalises across
  any text-serialisable genotype — code, configurations, JSON, DSL
  expressions, even sentences and image prompts. Establishes the
  theoretical and empirical case for LLMs as a general-purpose
  variation operator; the methodological foundation for the two
  scaled systems below and for any "evolve a JSON config" or
  "evolve a DSL program" pipeline that uses an LLM.
- [AlphaEvolve](alphaevolve.md) — Novikov et al., Google DeepMind
  2025. Scaled-up successor to FunSearch (Romera-Paredes et al.,
  Nature 2024) — full-file program evolution, state-of-the-art LLM
  ensemble, hours-long evaluators, parallel infrastructure, multiple
  metrics. Found a 4×4 complex matrix multiplication algorithm with
  48 scalar multiplications, improving on Strassen's 1969 bound (the
  first sub-cubic matrix-multiplication method) in that setting, and
  produced advances on several open problems in mathematics. Uses
  [island populations](../concepts/parallel-and-distributed-ga.md)
  inherited from FunSearch.
- [ShinkaEvolve](shinkaevolve.md) — Lange, Imajuku & Cetin, Sakana
  AI 2025. Open-source, sample-efficient counterpart to AlphaEvolve.
  Introduces three mechanisms designed to make each LLM call count:
  power-law parent sampling that biases toward both high-performing
  and high-novelty parents; rejection sampling on a learned
  code-embedding novelty signal; and a multi-armed bandit that learns
  which LLM to use when. State-of-the-art results in hundreds of
  samples rather than thousands. Cites Tanese 1989 directly for its
  island structure.

## Evolutionary search over LLM prompts

A lineage that takes the question of "what should we put in front of a
fixed LLM at inference time?" and answers it with population-based
evolutionary search. Two complementary subpatterns coexist. In the
first, exemplified by EPPO below, the LLM is held *outside* the search
loop as the fitness oracle and a classical comparison-based discrete
EA is the variation operator over an integer-index genome — the
methodological mirror of the LLM-driven program-evolution thread above,
with the bonus of an information-theoretic generalization bound from
the coarse comparison-only feedback signal. In the second, exemplified
by Promptbreeder, the LLM is itself the variation operator on a
natural-language genome — the prompt-flavoured analogue of the
LLM-as-mutator program-evolution lineage, with the additional twist
that the *mutation-prompts* are themselves under selection in a
self-referential loop.

- [Promptbreeder](promptbreeder.md) — *"Promptbreeder: Self-Referential
  Self-Improvement via Prompt Evolution"*, Fernando, Banarse,
  Michalewski, Osindero & Rocktäschel, Google DeepMind 2023 preprint /
  ICML 2024. Evolves a population of (task-prompt, mutation-prompt)
  pairs with PaLM 2-L acting as the variation operator on both. Nine
  mutation operators across five classes — direct, estimation-of-
  distribution, hyper-mutation of the mutation-prompt itself,
  Lamarckian working-out-to-task-prompt, and prompt
  crossover / context shuffling — driven by binary-tournament selection
  on a population of 50 over 20–30 generations. Reaches 83.9% zero-shot
  on GSM8K with the unintuitive evolved task-prompt `"SOLUTION:"`,
  surpassing OPRO and Plan-and-Solve on identical underlying LLMs;
  generalises beyond mathematical reasoning to ETHOS hate-speech
  classification at 89% (vs 80% hand-designed). The canonical reference
  for *self-referential* prompt evolution.
- [EPPO](eppo.md) — *"Evolutionary Pre-Prompt Optimization for
  Mathematical Reasoning"*, Videau, Leite, Schoenauer & Teytaud, 2024
  preprint / 2026 v2. Encodes a few-shot Chain-of-Thought pre-prompt as
  a length-s integer array indexing a demonstration pool, optimizes it
  with a Nevergrad comparison-based EA (Discrete (1+1)-ES, Portfolio,
  DiscLengler, …), and proves a `Pr(|L̂(r) − L(r)| > ε) ≤ κ^b · δ_{1,ε}`
  generalization-risk bound that depends only on the optimizer feedback
  arity κ and budget b — not on the LLM's capacity, the ambient
  pre-prompt space `|D|^s`, or the few-shot size s. Empirically
  improves LLaMA2-70B exact-match by more than 10 points on GSM8k and
  MathQA over standard CoT, with gains additive on top of
  self-consistency, while keeping the compute footprint well below
  fine-tuning.

## Evolutionary merging of trained weights

A complementary thread to the LLM-driven *code* evolution above.
Instead of evolving the source code of a program, evolve the
*weights* of a pretrained neural network — or, more precisely, evolve
the *recipes* that combine the weights of multiple pretrained
networks. Reproduction is arithmetic and orders of magnitude cheaper
than gradient-based training per offspring.

- [Model Merging](model-merging.md) — *"Evolutionary Optimization of
  Model Merging Recipes"*, Akiba et al., Sakana AI 2024 (later
  Nature Machine Intelligence). Treats the parameters of multiple
  pretrained models as the genome and uses
  [CMA-ES](../concepts/cma-es.md) — the canonical
  continuous-domain evolution strategy that adapts a multivariate
  Gaussian sampler from success feedback — to search the continuous
  merging coefficients. Searches in two complementary spaces:
  parameter space (interpolation weights, sparsification parameters)
  and data-flow space (which layers from which source model appear in
  the composite). Produces a Japanese-language math model by merging
  an English math model with a general Japanese model, with no
  gradient descent in between. A clean application of CMA-ES outside
  its usual robotics-and-control settings.

## Index by slug

The same set, alphabetically, with the citation header and lineage
tag for quick lookup:

| Slug | Title | Authors | Year | Venue | Lineage |
|------|-------|---------|------|-------|---------|
| [alphaevolve](alphaevolve.md) | AlphaEvolve: A Coding Agent for Scientific and Algorithmic Discovery | Novikov, Vũ, Eisenberger et al. | 2025 | Google DeepMind white paper | LLM-driven program evolution |
| [alphazero](alphazero.md) | Mastering Chess and Shogi by Self-Play with a General Reinforcement Learning Algorithm | Silver, Hubert, Schrittwieser et al. | 2017 / 2018 | arXiv / Science | self-play coevolution |
| [eppo](eppo.md) | Evolutionary Pre-Prompt Optimization for Mathematical Reasoning | Videau, Leite, Schoenauer, Teytaud | 2024 / 2026 | arXiv preprint | evolutionary search over LLM prompts |
| [grammatical-evolution](grammatical-evolution.md) | Grammatical Evolution | O'Neill, Ryan | 2001 | IEEE Transactions on Evolutionary Computation 5(4) | grammar-constrained EA |
| [island-ga](island-ga.md) | The Distributed Genetic Algorithm Revisited | Belding | 1995 | ICGA-95 | parallel / distributed GA |
| [lmx](lmx.md) | Language Model Crossover: Variation through Few-Shot Prompting | Meyerson, Nelson, Bradley, Gaier, Moradi, Hoover, Lehman | 2023 / 2024 | arXiv / ACM TELO | LLM-driven evolution (foundational) |
| [mcc](mcc.md) | Minimal Criterion Coevolution: A New Approach to Open-Ended Search | Brant, Stanley | 2017 | GECCO | neuroevolutionary open-ended coevolution |
| [model-merging](model-merging.md) | Evolutionary Optimization of Model Merging Recipes | Akiba, Shing, Tang, Sun, Ha | 2024 | Sakana AI / Nature Machine Intelligence | evolutionary merging of trained weights |
| [paired](paired.md) | Emergent Complexity and Zero-shot Transfer via Unsupervised Environment Design | Dennis, Jaques, Vinitsky et al. | 2020 | NeurIPS | deep RL / UED |
| [plr](plr.md) | Prioritized Level Replay | Jiang, Grefenstette, Rocktäschel | 2021 | ICML | deep RL / UED (replay-based) |
| [promptbreeder](promptbreeder.md) | Promptbreeder: Self-Referential Self-Improvement via Prompt Evolution | Fernando, Banarse, Michalewski, Osindero, Rocktäschel | 2023 / 2024 | arXiv preprint / ICML 2024 | evolutionary search over LLM prompts |
| [shinkaevolve](shinkaevolve.md) | ShinkaEvolve: Towards Open-Ended and Sample-Efficient Program Evolution | Lange, Imajuku, Cetin | 2025 | Sakana AI tech report | LLM-driven program evolution |

## Related wiki pages

- [../concepts/concepts.md](../concepts/concepts.md) — the
  cross-cutting design-space register; one page per modification of
  the canonical loop.
- [../foundations/foundations.md](../foundations/foundations.md) — the
  canonical loop and the broader EC family.
- [../overview.md](../overview.md) — the same story told end to end.
