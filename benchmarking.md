# Benchmarking & Validation

This repository does not publish benchmark numbers of its own. This note explains why, and what to look for when evaluating a claimed result.

## The core distinction: four kinds of evidence

| Tier | What it means | How to treat it |
|---|---|---|
| **Paper-reported result** | A number reported in a publication, on that group's own data/setup | Real evidence, but not independently verified — check the methods section for what exactly was measured |
| **Reproduced result** | Someone outside the original group ran the released code/data and got a comparable number | Stronger — this is what repositories like `speechBCI` and `nejm-brain-to-text` are built to enable |
| **Standardized benchmark** | A shared task/dataset/metric that multiple groups report against (e.g., a public leaderboard or competition) | Enables real comparison between methods, if the task is representative of real use |
| **Real-time / closed-loop benchmark** | Measured under actual online, closed-loop conditions — not retrospective/offline simulation on previously recorded data | The gold standard for this repository's scope, and the rarest — offline accuracy is a known weak proxy for online closed-loop performance (see Recipe 1) |

Most published neural decoding results are Tier 1 (paper-reported) or, for the strongest work in this repository, Tier 2 (reproducible via released code). Standardized, cross-lab benchmarks (Tier 3) exist for some sub-problems (e.g., the BCI Competition datasets for motor imagery/P300/SSVEP classification, and the annual Brain-to-Text competition built on the `nejm-brain-to-text` codebase). True closed-loop, real-time benchmarks (Tier 4) are the least standardized part of the field — this is itself one of the [Open Questions](open-questions.md).

## What makes a benchmark trustworthy for closed-loop work specifically

1. **It was measured online, not just offline.** A decoder's cross-validated offline accuracy does not reliably predict its closed-loop performance, because the human adapts to the decoder while using it (see [Control & Adaptation](control-and-adaptation.md)). Treat offline-only benchmarks as necessary but not sufficient evidence.
2. **It reports variability, not just a point estimate.** Across-subject and across-session variance in this field is typically large. A single mean number without a distribution or per-subject/per-session breakdown is easy to over-interpret.
3. **It states the calibration/training data budget.** A comparison between two decoders is only fair if both had access to comparable training data — an unfairly-favored comparison is one of the more common ways benchmark claims mislead.
4. **The code and, ideally, data are released.** This is what separates Tier 1 from Tier 2 above, and it's the single strongest trust signal available to an outside reader.

## Existing standardized resources in this space

- **[MOABB](https://github.com/NeuroTechX/moabb)** — the closest thing this field has to a rigorous, reproducible, standardized benchmark: one API across dozens of public EEG datasets, with built-in within-session, cross-session, and cross-subject evaluation modes, backed by a dedicated reproducibility study (Chevallier et al., "The largest EEG-based BCI reproducibility study for open science"). This is the single strongest standardized-benchmark resource identified for this repository.
- **BCI Competition datasets** — the long-running standard benchmark tasks for motor imagery, P300, and SSVEP EEG classification, several of which MOABB wraps directly.
- **Brain-to-Text Competition** — built directly on the [`nejm-brain-to-text`](https://github.com/Neuroprosthetics-Lab/nejm-brain-to-text) codebase and data (hosted on Kaggle), giving the speech-decoding sub-field a genuine shared benchmark with released baseline code.
- **LINK dataset** — purpose-built to standardize evaluation of decoder stability across long (year-scale) timespans for intracortical BMIs, addressing the cross-session generalization problem directly (see [Datasets](../data/README.md)).

## What we will not do

We will not publish comparative "X is faster/more accurate than Y" claims in this repository unless they come with a citable, verifiable source, because doing otherwise would misinform the exact audience this repository exists to help. Where a recipe references a performance number (e.g., Recipe 3's word-error-rate figures), it is sourced directly to the paper and presented as that paper's reported result — not as a general truth about the field.
