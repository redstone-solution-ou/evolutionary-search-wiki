# CLAUDE.md — Schema for the Evolutionary Search Wiki

This file is the working contract for a Claude Code (or equivalent) session
operating on this repository. Read it before touching anything. The generic
LLM-wiki pattern this schema instantiates is in
[llm-wiki.md](llm-wiki.md); that file describes the idea in the abstract.
This file is the project-specific conventions, templates, and workflows.

## Project identity

A research wiki on **evolutionary search**: classical genetic algorithms
and parallel/distributed variants, coevolution, open-ended evolution,
unsupervised environment design (UED), and LLM-driven program evolution.
It tracks the literature that uses population-based stochastic search —
sometimes with multiple coevolving populations, sometimes with an LLM
as the variation operator, sometimes with an environment-generating
adversary — to drive emergent complexity, quality diversity, automatic
curricula, novel algorithmic discovery, and zero-shot generalization.
The reader is a researcher entering the field who wants to understand
how the classical EC lineage (Tanese, NEAT, novelty search, MAP-Elites,
MCC), the deep-RL lineage (PAIRED, POET, ACCEL), and the modern
LLM-evolution lineage (FunSearch, AlphaEvolve, ShinkaEvolve) connect,
and which paper to cite for which idea.

## Architecture

Three layers. Each has a distinct lifetime and ownership discipline.

- **`papers/`** — raw source layer. Immutable PDFs downloaded from arXiv,
  ACM, or author-hosted proceedings. Named `<shortname>_<id>.pdf` where
  `<id>` is the arXiv identifier when available, otherwise
  `<authors>_<year>`. Never modify. Never delete. New ingests go here
  first.
- **`wiki/`** — the wiki proper. LLM-owned markdown. Every claim is either
  sourced from a paper in `papers/` (cited) or follows from another wiki
  page (linked). This layer is where all the work happens.
- **`CLAUDE.md`** (this file) — schema / conventions / workflows.
  Co-evolves with the wiki; update it whenever a convention changes.

## Top-level layout

```
evolutionary-search-wiki/
├── CLAUDE.md                   (this file — schema)
├── llm-wiki.md                 (reference copy of the generic pattern)
├── README.md                   (repo landing page)
├── .gitignore
├── papers/                     (immutable PDFs)
│   └── <shortname>_<id>.pdf
└── wiki/
    ├── overview.md             (orientation and reading paths)
    ├── index.md                (flat catalog of every wiki page)
    ├── log.md                  (chronological append-only log)
    ├── foundations/
    │   ├── foundations.md      (section hub)
    │   ├── genetic-algorithms.md
    │   └── evolutionary-computation.md
    ├── concepts/
    │   ├── concepts.md         (section hub)
    │   └── ... concept pages
    └── papers/
        ├── papers.md           (section hub / paper index)
        └── <slug>.md           (one per paper)
```

**Naming convention (folder-note pattern):** there is exactly ONE
`README.md` in the entire repo, at the root. Every wiki section uses
a folder-note named after the section itself (`foundations.md`,
`concepts.md`, `papers.md`) as its hub. The wiki's own hub is
`overview.md`. This keeps the Obsidian graph view readable — every
node has a descriptive name, no collisions on the label "README".

**Future sections (when the wiki grows):** `architectures/` (e.g.
agent-environment adversarial loop, dual-queue coevolution, novelty
archives), `benchmarks/` (e.g. MiniGrid maze suite, BipedalWalker hardcore,
NetHack MiniHack), `evaluation/` (e.g. zero-shot transfer protocols,
diversity metrics), `research/` (open problems, comparison matrix,
reading roadmap). Add these as paper count grows past ~5. Until then,
keep the structure flat.

## Page types and templates

Every page belongs to one of the types below. Use the template literally
when creating or rewriting. Do not invent new page types without updating
this file first.

### 1. Paper leaf — `wiki/papers/<slug>.md`

One per paper. Slug is the lowercase hyphen-separated short name
(`paired`, `mcc`, `poet`, `accel`). Target length 600–1200 words,
dense, no padding. **A leaf is a faithful technical mirror of the paper's
substantive content** — every design rationale the paper itself motivates
(in §body, appendix, or figure caption) belongs on the leaf, not just the
component inventory. If you find yourself bouncing to a concept page to
answer "*why* does this paper's component X exist?", the leaf has a gap
and must be fixed.

```markdown
# <Full Paper Title>

> **Short name:** `<slug>` · **arXiv:** [<id>](https://arxiv.org/abs/<id>) · **PDF:** [local](../../papers/<filename>.pdf) · **Date:** <YYYY-MM> · **Venue:** <venue or preprint>

**Authors:** <first 3> et al.

## Abstract
(one paragraph; verbatim or lightly paraphrased from the paper)

## Key contributions
- bullet
- bullet

## Method at a glance
(One short paragraph — algorithm family, what populations / agents are
involved, training signal, the central mechanism, AND the rationale
behind each non-trivial design choice the paper itself motivates. If
the paper devotes a paragraph, appendix section, or figure caption to
justifying a component — choice of selection operator, mutation rate,
role of a coevolutionary partner, why this curriculum, why this archive
structure, why this regret estimator, why this LLM-as-mutator prompt —
mirror that rationale here. Inventory without rationale is not enough;
the leaf is a technical mirror of the paper's substantive design, not a
parts list. If a single design choice carries multi-paragraph
justification in the paper, promote it to its own H3 subsection inside
this section, e.g. `### How <method> prevents premature convergence` or
`### Why <method> uses minimal-criterion coevolution`.)

## Why it matters
(2-3 sentences on what this paper moved forward)

## Strengths
- at least 3 bullets — concrete things the paper demonstrates

## Limitations and open critiques
- at least 3 bullets — what the paper admits, what follow-ups dispute,
  structural gaps (domain choice, scale evidence, reproducibility)

## Follow-up work and dialogue
(1-2 paragraphs on what papers build on, dispute, or supersede this one.
Cross-link paper leaves by slug.)

## Reproducibility
- **Open code:** yes / no / partial, where (only if paper states it)
- **Domains used:** what experimental environment(s)
- **Compute disclosed:** quote any GPU-hours / CPU-hours / wall-clock
  the paper gives; otherwise "not disclosed"
- **Hyperparameters:** key ones the paper reports (population sizes,
  batch sizes, training steps); otherwise "not disclosed"

## When to cite this paper
(2-3 sentences: what specific claim is this the canonical reference for,
so a reader knows when to pick this paper vs a successor)

## In the knowledge graph
- **Related concepts:** [<page>](../concepts/<page>.md), ...
- **Foundations:** [<page>](../foundations/<page>.md)
- **See also:** [<paper>](./<other>.md)
```

### 2. Concept page — `wiki/concepts/<slug>.md`

A cross-cutting technical idea that multiple papers instantiate
differently (coevolution, open-ended evolution, minimal criterion,
novelty search, regret as objective, automatic curriculum, ...).
Target length 700–1200 words.

```markdown
# <Concept Name>

<1-2 sentence definition understandable to a reader with general ML background>

## Intuition
(one paragraph — what the idea is, why it exists, what it replaces)

## Mechanics
(concrete algorithm / formula / pseudocode; show the input → output
transformation)

## Why it works
(inductive bias, invariance, statistical property, optimization
advantage, or evolutionary dynamic; tie to general principles)

## Trade-offs and failure modes
(where it breaks, what it gives up, ablation evidence)

## Design choices in the literature
(how specific papers instantiated this differently)

## Open questions
- 3-5 real frontier research questions (not filler)

## Papers that exemplify this
- [<paper>](../papers/<slug>.md) — 1-line note on *how* it uses this

## Related wiki pages
- [<page>](../concepts/<other>.md)
- [<page>](../foundations/<other>.md)
```

Must cross-link to ≥2 other concepts and ≥1 foundation page (until
`architectures/` exists, after which the rule expands to ≥2 architecture
pages).

### 3. Foundations page — `wiki/foundations/<slug>.md`

A classical or precursor topic that grounds the modern literature
(genetic algorithms, evolutionary computation, neuroevolution, NEAT,
RL background). Same template as a concept page, but with an
explicit "Where this leads" section pointing to the modern descendants.

### 4. Section hub (folder note) — `wiki/<section>/<section>.md`

Each wiki section has a hub page named after the section itself:
`foundations/foundations.md`, `concepts/concepts.md`,
`papers/papers.md`. One-paragraph orientation plus a bullet list with
a one-line gloss per sub-page. Target 120–180 words. No content beyond
the index. There are NO files named `README.md` inside `wiki/`; the
only `README.md` in the repo is the one at the root.

### 5. Wiki root files

- `wiki/overview.md` — entry point, orientation, "start here" reading
  paths, knowledge-graph sketch.
- `wiki/index.md` — flat catalog of every wiki page, one line each.
  First stop at query time. Must be kept in sync with every add /
  rename / delete.
- `wiki/log.md` — chronological append-only record of ingests,
  queries filed back, lint passes, and refactors.

## Style rules

- **Avoid acronyms where natural prose works.** Prefer the full
  phrase (`"genetic algorithm"`, `"reinforcement learning"`,
  `"large language model"`, `"quality diversity"`, `"minimal
  criterion"`) over the acronym (`"GA"`, `"RL"`, `"LLM"`, `"QD"`,
  `"MC"`). The wiki reads better and is more accessible to readers
  outside the immediate specialty. Exceptions are limited to:
  (a) proper names of specific algorithms or systems where the
  acronym *is* the established name in the literature — `NEAT`,
  `PAIRED`, `MCC`, `POET`, `ACCEL`, `CMA-ES`, `MAP-Elites`, etc.
  (these are titles, not abbreviations, and should be used as such);
  (b) places where repeating the full phrase would make the text
  unreadable (very long compound names used many times in one
  paragraph). When an acronym is used outside those exceptions, it
  must still be expanded on first use per page.
- **Cite papers with full titles, not filenames.** Whenever a wiki
  page references a paper by its local PDF path, the paper's full
  title, authors, and year must appear in adjacent prose. File paths
  like `papers/foundations/poet_wang_1901.01753.pdf` are opaque to a
  reader; the filename is not a citation. Preferred form:
  `*"Paper Title"* — Authors, Venue Year, arXiv:ID. Local: [PDF](path).`
  Applies to foundation-reference PDFs (papers without dedicated
  leaves) and to any ad-hoc PDF citation. For papers with a
  dedicated leaf, link to the leaf instead — the leaf carries the
  title.
- **No emojis.** Anywhere.
- **No fabricated numbers.** Every numeric claim cites a specific paper's
  table, figure, or section, or a public source with an access date.
  Where a value is not disclosed, write "not disclosed" and say so in a
  caveat.
- **No marketing language.** No "revolutionary", "game-changing",
  "state-of-the-art" without citation.
- **Relative links for internal navigation.** Never absolute URLs to
  other wiki pages.
- **First occurrence of a paper short name in prose is linked to its
  leaf** (`[PAIRED](../papers/paired.md)`). Subsequent occurrences are
  plain text. Same for concept names — first mention links, later
  mentions are plain.
- **Every page ends with a "Related wiki pages" section** with ≥2
  cross-links.
- **Math in backticks** for inline (`U(π) = Σ r_t γ^t`) or `$$...$$`
  fenced blocks for display (GitHub renders both).
- **Fenced code blocks** for pseudocode and algorithm walkthroughs.
- **Research tone.** Direct, qualified, willing to write "disputed",
  "not comparable", "not disclosed".
- **ATX headings** (`##`), not setext. Title case for H1, sentence case
  for H2+ by default.
- **American English spelling**, Oxford commas OK.

## Cross-link discipline

- Concept → concept: ≥2
- Concept → foundation: ≥1
- Foundation → concept: ≥2
- Paper leaf → complete "In the knowledge graph" block (related concepts,
  foundations, see-also)
- Broken relative links are a lint failure. Fix on sight.
- When you rename or delete a page, grep for inbound links and update
  them in the same commit.

## Workflows

### Ingest: adding a new paper

1. Download PDF to `papers/<shortname>_<id>.pdf` (descriptive
   User-Agent, polite spacing between requests).
2. Read the PDF: abstract, method section, experiments, related work,
   limitations / discussion. Extract title, authors, date, venue,
   key contributions, mechanism, strengths, limitations,
   reproducibility facts.
3. Create leaf at `wiki/papers/<slug>.md` using the paper-leaf template.
4. Update `wiki/papers/papers.md` index table and grouping.
5. Update the relevant concept and foundation pages to mention the new
   paper in their "Papers that exemplify this" sections.
6. If the paper introduces a new concept that does not yet have a page,
   create `wiki/concepts/<slug>.md` using the concept template.
7. Update `wiki/index.md` with the new leaf entry and any new concept.
8. Append to `wiki/log.md`:
   `## [YYYY-MM-DD] ingest | <paper short name>` + 2-3 sentences.
9. Commit on a feature branch and open a PR.

### Query: answering a user question

1. First stop: `wiki/index.md`. Identify candidate pages.
2. Read those pages plus any referenced paper leaves.
3. Answer with citations (paper leaf, table or figure, or wiki page link).
4. **If the answer is non-trivial and generally useful**, file it back
   as a new wiki page under the appropriate section (`concepts/`,
   `foundations/`, or once they exist `research/` or `benchmarks/`).
   Update `wiki/index.md`. Append to `wiki/log.md`:
   `## [YYYY-MM-DD] query-filed-back | <topic>` + 1-2 sentences.
5. If the answer was trivial fact retrieval (one number, one definition),
   do NOT create a page. Chat-only.

### Lint: periodic health check

Run when asked, or after a batch of ingests. A full lint pass checks:

- **Orphan detection** — pages with zero inbound links from elsewhere in
  `wiki/`. Either link from a relevant parent page or delete.
- **Broken relative links** — every `../x.md` must resolve.
- **Template compliance** — paper leaves missing required sections,
  concept pages missing "Related wiki pages" or the cross-link minimum.
- **Unlinked paper mentions** — `paired`, `mcc`, etc. that appear in
  prose without a relative link on first occurrence.
- **Unexpanded acronyms** — any acronym whose first occurrence in a
  page is not spelled out with the expansion in parentheses.
- **Unattributed numbers** — grep for `%|×|x|hours|steps` in prose
  lines that don't contain `(...)` citations.
- **Concept gaps** — terms that appear ≥3 times across pages but have
  no dedicated concept or foundation page. Propose a new page.
- **Contradictions** — claims that disagree across pages. Flag in the
  lint report even if you can't resolve them.
- **Design-rationale gaps in leaves** — for each paper leaf, check
  whether the paper's body or appendix devotes space to motivating
  a design choice (choice of selection operator, mutation rate,
  coevolutionary partner role, curriculum design, archive structure,
  regret estimator, LLM-as-mutator prompt) that the leaf merely
  *names* without *explaining*. Flag leaves that fail the test "a
  reader of this leaf alone can answer *why* component X is there."
  Treat each gap as a fix-on-sight task.
- Append `## [YYYY-MM-DD] lint | <summary>` to `wiki/log.md` with
  counts and links to the fixes.

## Anti-patterns (do NOT do these)

- Do not fabricate numbers, URLs, model sizes, or hyperparameters.
- Do not add emojis.
- Do not create marketing-tone prose.
- Do not duplicate content across pages; cross-link instead.
- Do not write a paper leaf that omits a design rationale the paper
  itself motivates. If the paper has a paragraph, appendix section,
  or figure caption explaining WHY a component is there (choice of
  selection operator, mutation rate, role of a coevolutionary partner,
  why this curriculum, why this archive structure, why this regret
  estimator, why this LLM-as-mutator prompt), that rationale must
  appear on the leaf — not just the inventory. Test: a reader of the
  leaf alone should be able to answer "*why* does this paper's
  component X exist?" without bouncing to the concept page or the PDF.
- Do not write to `papers/` — the raw source layer is immutable.
- Do not use absolute URLs for internal wiki navigation.
- Do not leave a page without a "Related wiki pages" block.
- Do not edit `llm-wiki.md` as part of routine ingest, query-filed-back,
  or lint work; it is the reference copy of the generic pattern.
  Schema changes that are project-specific go in this file (`CLAUDE.md`).
  Structural clarifications to the *generic* pattern itself (something
  true of any LLM-built wiki, not just this one) may go into
  `llm-wiki.md` only with explicit user direction, and should be logged
  with a `schema` op in `wiki/log.md`.
- Do not commit auto-generated helper scripts (put them in `/tmp/`).
- Do not touch `.obsidian/` or `.DS_Store` (gitignored).
- Do not amend an already-pushed commit.
- Do not commit directly to `main`. Use feature branches and pull
  requests. **Each novelty is its own short-lived branch, merged as
  soon as it is complete** — do not accumulate many independent
  novelties on one branch and merge them later in a single batch.
  The correct flow is: create branch, make one focused change (one
  ingest, one concept page, one sweep, one lint pass), commit, merge
  to `main`, delete the branch, start the next branch. A branch
  that has stayed open across multiple unrelated novelties is a
  process violation and should be split into its actual novelties
  before merge.
- Do not skip log entries for non-trivial changes. The log is how
  future sessions understand what happened.

## Canonical paper slugs (as of 2026-04)

Twelve paper leaves exist. Use these slugs in all cross-links:

```
alphaevolve   alphazero   eppo   grammatical-evolution   island-ga   lmx   mcc   model-merging   paired   plr   promptbreeder   shinkaevolve
```

When ingesting a new paper, pick a slug by lowercasing the model or
algorithm short name and hyphenating. Likely future additions to seed
into the slug table when they are ingested: `funsearch`, `poet`,
`enhanced-poet`, `accel`, `plr`, `dual-curriculum-design`,
`openevolve`, `codeevolve`, `eureka`, `darwin-godel-machine`, `neat`,
`novelty-search`, `map-elites`, `cma-es`.

## Getting started in a new session

1. Read this file.
2. Read `wiki/index.md` to see what exists.
3. Read `wiki/log.md` tail to see what was done recently.
4. Do the work.
5. Update `wiki/index.md` and `wiki/log.md` before committing.
