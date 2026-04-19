# Covariance Matrix Adaptation Evolution Strategy (CMA-ES)

Covariance Matrix Adaptation Evolution Strategy (CMA-ES) is a
continuous-domain black-box optimizer that samples offspring from a
multivariate Gaussian `Normal(m, σ²·C)` and *adapts* the mean `m`, step
size `σ`, and covariance matrix `C` each generation so that the sampling
distribution tracks the shape of the fitness landscape. It has been the
strong default for continuous black-box optimization in moderate
dimensions for more than two decades.

## Intuition

Plain Evolution Strategies (ES) use isotropic Gaussian mutation: add
`σ·N(0, I)` to the current candidate. That is equivalent to searching
inside a ball of radius `σ` around the mean. On a landscape with a ridge
— fitness grows along one narrow direction and is flat or worse
elsewhere — an isotropic ball wastes most of its samples. A smarter
strategy would stretch the ball into an ellipsoid aligned with the
ridge, so most samples land along the useful direction.

The covariance matrix `C` is exactly that ellipsoid. Its eigenvectors
point in the directions the algorithm currently believes are promising;
its eigenvalues are the squared lengths of the ellipsoid's semi-axes.
CMA-ES's central trick is to *learn* `C` from success feedback: after
each generation, update `C` so that future samples cluster in the
directions that produced the best offspring. No gradient is ever
computed; the algorithm extracts second-order information — something
like an inverse Hessian — from the success pattern alone.

## Mechanics

One generation consists of sampling, selection, and three coupled
updates.

### Sampling

Generate `λ` offspring around the current mean `m` by drawing from the
full-covariance Gaussian:

```
x_i = m + σ · z_i,    z_i ~ Normal(0, C),    for i = 1..λ
```

Typical defaults: `λ = 4 + ⌊3 ln D⌋` where `D` is the search-space
dimension. For `D = 100`, `λ ≈ 17`.

### Selection

Rank the `λ` offspring by fitness. Keep the top `μ` (typically `μ = λ/2`
= 8 in our example). Assign weights `w_i` that decrease with rank —
the standard choice is `w_i ∝ log(μ + 1) − log(i)` normalized so
`Σ w_i = 1`. Compute:

- the new mean: `m_new = Σ_i w_i · x_i`
- the "effective selection mass": `μ_eff = 1 / Σ w_i²` (between `μ/4`
  and `μ`; measures how concentrated the weights are).

### Rank-μ update — the Principal Component Analysis (PCA) piece

Treat the *weighted deviations of the selected offspring from the old
mean* as a small dataset and compute its weighted empirical covariance:

```
C_rank_μ = Σ_i w_i · ((x_i − m) / σ) · ((x_i − m) / σ)^T
```

The `/σ` divides out the step size, so `C_rank_μ` captures *shape* only,
not magnitude. This expression is a weighted, rank-μ Principal Component
Analysis of the successful offspring relative to the mean — the very
PCA-like piece of CMA-ES.

### Rank-1 update — memory across generations

A single generation of 8–20 winners is a noisy PCA estimate in `D`
dimensions. CMA-ES reduces that noise by also tracking an **evolution
path** `p_c` — an exponential moving average of how the mean has been
moving across generations:

```
p_c_new = (1 − c_c) · p_c_old + √(c_c · (2 − c_c) · μ_eff) · (m_new − m_old) / σ
```

Then add the rank-1 outer product:

```
C_rank_1 = p_c · p_c^T
```

`p_c · p_c^T` is a rank-1 matrix that boosts variance specifically along
the direction the mean has been drifting for many generations. This
component has no direct PCA analogue — it is a *temporally correlated*
signal, picked up from the trajectory of `m`, not from the spread of
one generation.

### Combined covariance update

Blend the old `C` with the two new terms via small learning rates:

```
C_new = (1 − c_1 − c_μ) · C_old
      + c_1 · p_c · p_c^T
      + c_μ · C_rank_μ
```

Default learning rates: `c_1 ≈ 2 / D²`, `c_μ ≈ μ_eff / D²`. Both are
small, so `C` changes slowly. In practice this matters — large
learning rates make the algorithm unstable on ill-conditioned
landscapes.

### Step-size update — Cumulative Step-size Adaptation (CSA)

The step size `σ` is adapted by a separate rule using its own evolution
path `p_σ`. Roughly: if the evolution path is longer than the expected
length under a random walk, the algorithm has been moving consistently
forward and `σ` should grow; if shorter, `σ` should shrink. The exact
formulas are in Hansen's 2016 tutorial (arXiv:1604.00772); the
intuition is that `σ` tracks the *scale* of useful moves while `C`
tracks their *shape*.

## Why it works

The PCA analogy is close but not exact.

| Feature | Naive PCA | CMA-ES |
|---|---|---|
| Data used | all samples, equal weight | top `μ` offspring, rank-weighted |
| Centering | around sample mean | around *current distribution* mean, before offspring were scored |
| Temporal structure | none (one-shot) | exponential moving average over generations |
| Temporal coherence signal | none | rank-1 evolution path |
| Normalization | raw covariance | `/σ` to separate shape from scale |

The combined effect: `C` converges (up to a scalar) to something
proportional to the *inverse Hessian* of the local fitness landscape —
a second-order acceleration achieved without any gradient query. This
is the source of CMA-ES's well-known invariance properties: order-
preserving transformations of the fitness and rotations / translations
/ scalings of the search space leave the algorithm's behavior
unchanged.

## Trade-offs and failure modes

- **Cost per step.** The default covariance representation is a
  full `D × D` matrix, and sampling requires an eigendecomposition
  every few generations. This is `O(D³)`. At `D = 1000` it starts to
  hurt; at `D = 10 000` it is prohibitive. Variants (see next
  section) address this.
- **Selection pressure is fixed by `λ, μ, w_i`.** There is no adaptive
  selection pressure; on very rugged landscapes a fixed schedule can
  be too aggressive or too cautious.
- **Sensitivity to initialization.** Starting `m` in a bad basin and
  starting `σ` too small can trap CMA-ES in a local optimum before
  the covariance has time to adapt. Restart strategies are standard
  practice.
- **Not for combinatorial domains.** CMA-ES is specifically a
  continuous-domain algorithm. Binary or discrete spaces need
  different methods (see the Genetic Algorithm page).
- **Does not beat Differential Evolution on very high-dimensional or
  noisy benchmarks.** The Linear Population Size Reduction SHADE
  (L-SHADE) variant of Differential Evolution has dominated the IEEE
  Congress on Evolutionary Computation (IEEE CEC) competitions since
  2014 on certain problem classes; CMA-ES and L-SHADE are
  complementary rather than strictly ordered.

## Design choices in the literature

- **CMA-ES** (Hansen & Ostermeier 2001; Hansen 2016 tutorial,
  arXiv:1604.00772) — the canonical reference. Full covariance,
  weighted rank-μ + rank-1 updates, CSA step-size adaptation.
- **sep-CMA-ES** (Ros & Hansen 2008) — restricts `C` to be diagonal.
  Drops cost from `O(D²)` per step to `O(D)`; works well on
  separable-ish problems where cross-coordinate correlation is weak.
- **BIPOP-CMA-ES** (Hansen 2009) — restart strategy that alternates
  between small and large population sizes to escape local optima.
- **Active CMA-ES** (Jastrebski & Arnold 2006) — uses negative weights
  on the worst offspring to *shrink* `C` in bad directions,
  accelerating adaptation.
- **L-BFGS-CMA-ES and LM-CMA-ES** — memory-limited variants that keep
  only a low-rank approximation of `C`, usable up to `D ≈ 10 000`.
- **Covariance Matrix Adaptation MAP-Elites (CMA-ME)** (Fontaine et
  al. 2020) — CMA-ES plugged into MAP-Elites. See below.

## Plugging CMA-ES into MAP-Elites — CMA-ME

Multi-dimensional Archive of Phenotypic Elites (MAP-Elites; Mouret &
Clune 2015) is a Quality Diversity (QD) algorithm with a grid of
behavior cells, one elite per cell, and isotropic Gaussian mutation of
a randomly-chosen elite. On continuous problems the isotropic mutation
is as wasteful for MAP-Elites as it is for plain ES — same story, same
fix. CMA-ME replaces the isotropic mutation with CMA-ES-style emitters:

- Each emitter has its own `(m_i, σ_i, C_i, p_c_i)`, initialized from a
  randomly-chosen elite.
- Per-step: sample `λ` offspring from `Normal(m_i, σ_i² C_i)`, evaluate
  their fitness and Behavior Characterization (BC), insert into the
  grid (replace the current elite if beating it, or fill an empty
  cell), update `(m_i, σ_i, C_i)` using the CMA-ES rules.
- **The definition of "success" for the covariance update is
  redefined.** In plain CMA-ES an offspring is a success if it has
  higher fitness than its peers. In CMA-ME an offspring is a success
  if it either (a) beats the current elite of the cell it landed in,
  *or* (b) fills a previously empty cell. The weighted rank-μ update
  then learns directions that produce *either* quality gains *or*
  grid-filling novelty.

Multiple emitters run in parallel, specializing on different regions
of the grid. The grid itself is the persistent store; emitters are
transient search distributions. CMA-ME more than doubles MAP-Elites'
QD-score on Fontaine's continuous-robot-control benchmarks. The
gradient-aware successor CMA-MEGA (Fontaine & Nikolaidis, 2021) is
now the de facto default in continuous-domain QD.

## Open questions

- **Non-stationary landscapes.** CMA-ES assumes the landscape is
  static over the adaptation horizon. For landscapes that move (e.g.
  coevolutionary settings), the covariance can adapt to a
  now-irrelevant shape.
- **Interaction with gradient information.** When gradients are
  available, should `C` incorporate them directly? CMA-MEGA does;
  whether this is optimal is open.
- **Very high dimensions.** Full CMA-ES does not scale past
  `D ≈ 10⁴`. Low-rank and separable variants help but lose the
  invariance properties that made CMA-ES attractive in the first
  place.
- **Combining CMA-ES with Large Language Model (LLM) mutation.** The
  modern [LLM-driven evolution](llm-driven-evolution.md) papers use
  discrete code-space mutations where CMA-ES does not directly apply,
  but the underlying question — "how do I adapt the variation
  operator's proposal distribution from success feedback?" — is the
  same. No published work yet transfers CMA-ES-style adaptation to
  LLM-mutation scheduling.

## Papers that exemplify this

- *"The CMA Evolution Strategy: A Tutorial"* — Nikolaus Hansen,
  2016, arXiv:1604.00772. The canonical modern reference; full
  derivation of the rank-μ, rank-1, and Cumulative Step-size
  Adaptation (CSA) updates. Local:
  [`papers/foundations/cma-es-tutorial_hansen_1604.00772.pdf`](../../papers/foundations/cma-es-tutorial_hansen_1604.00772.pdf).
- *"Covariance Matrix Adaptation for the Rapid Illumination of
  Behavior Space"* — Matthew C. Fontaine, Julian Togelius, Stefanos
  Nikolaidis, Amy K. Hoover, GECCO 2020, arXiv:1912.02400. The
  CMA-ME paper; CMA-ES as an emitter inside MAP-Elites. Local:
  [`papers/foundations/cma-me_fontaine_1912.02400.pdf`](../../papers/foundations/cma-me_fontaine_1912.02400.pdf).
- *"Illuminating search spaces by mapping elites"* — Jean-Baptiste
  Mouret, Jeff Clune, 2015, arXiv:1504.04909. The base algorithm
  CMA-ME accelerates. Local:
  [`papers/foundations/map-elites_mouret-clune_1504.04909.pdf`](../../papers/foundations/map-elites_mouret-clune_1504.04909.pdf).
- [ShinkaEvolve](../papers/shinkaevolve.md) — uses a
  bandit-based adaptation for the LLM ensemble selection, which can
  be read as a very distant relative of CMA-ES's
  "adapt-the-sampler-from-success-signal" philosophy applied to
  discrete mutation operators.

## Related wiki pages

- [../foundations/evolutionary-computation.md](../foundations/evolutionary-computation.md)
- [../foundations/genetic-algorithms.md](../foundations/genetic-algorithms.md)
- [novelty-and-quality-diversity.md](novelty-and-quality-diversity.md)
- [llm-driven-evolution.md](llm-driven-evolution.md)
