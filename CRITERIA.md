# Inclusion Criteria & Maintainer Policy

This document is the actual maintainer policy. The README stays short; this stays precise.

## The one question

> Does this resource materially help someone **understand, prototype, build, test, or deploy** a closed-loop neurotechnology system?

If you can't answer yes in one sentence tied to a specific pipeline stage or connector, it doesn't belong here.

## Licensing scope — what CC0 covers and what it doesn't

This repository's [LICENSE](../LICENSE) (CC0 1.0 Universal) covers only the original content created here: README text, category structure, curation descriptions, reference architectures, and everything in `docs/`. It does not, and cannot, apply to any linked external project — each retains whatever license its own maintainers chose. When adding an entry, note the linked project's license where it's known (as most existing entries already do, e.g. `· MIT`, `· BSD-3-Clause`) so a reader doesn't have to guess or assume CC0 extends to the thing being linked.

If a contribution ever includes substantial original software code (e.g., a validation script, a generator for the docs site discussed in `ROADMAP.md` Phase 3) rather than markdown, that code should carry its own explicit software license (typically MIT, to match this repository's low-friction reuse philosophy) rather than being folded into the CC0 dedication — CC0 is a public-domain dedication suited to creative/informational works, not a software license in the sense MIT/Apache-2.0/GPL are. As of this writing, this repository contains no such code.

## What qualifies

A resource qualifies if it meets **all** of:

1. **Pipeline relevance** — it clearly sits on (or spans, or connects) the sense → sync → process → decode → control → actuate → feedback loop.
2. **Has a working artifact** — a repository, package, dataset, or spec you can install/download/read today. Papers only qualify if they link a code repository.
3. **Openness** — source, data, or spec is publicly accessible without a sales call. Commercial platforms are allowed if they have public documentation and a real (even if paid) developer path — see "Commercial products" below.
4. **Not better served elsewhere** — if an existing awesome-list already curates this sub-area well (e.g., neural foundation models, BCI papers), we link to *that list* once, rather than duplicating its contents here.

## What does not qualify

- General neuroscience tooling with no real-time or closed-loop application (fMRI analysis suites, connectomics, general computational modeling)
- ML/DL papers with no code, or code with no clear tie to a decoding-in-the-loop use case
- Offline-only algorithms when an online-capable equivalent already exists in the same category
- Consumer hardware/apps with no public SDK or API
- Marketing pages, blog posts, and "top 10" listicles
- Duplicate coverage of a well-maintained existing list (link instead)
- General-purpose software/robotics tools with no neurotech-specific relevance (e.g., we don't list Gazebo, DVC, or generic ROS packages standalone — see [`docs/simulation/README.md`](simulation/README.md) for how we handle this: mention in context, don't catalog)
- Speculative or unverifiable capability claims — see "Unverifiable projects" below

## Resource type labels

Every entry carries one type label, chosen from:

`TOOL` · `FRAMEWORK` · `HARDWARE` · `DATASET` · `STANDARD` · `PAPER+CODE` · `PROTOCOL` · `SIMULATOR` · `BENCHMARK` · `REFERENCE LIST`

These exist because "what kind of thing is this" is often the first question a reader has, and it disambiguates entries that could otherwise be misread (e.g., MOABB is both a `TOOL` and functions as a `BENCHMARK` — we tag both when genuinely true). We do not add compatibility metadata (OS, exact hardware support matrices, etc.) inline in the README beyond the existing tags (`real-time-capable`, `invasive`/`non-invasive`, `research`/`clinical`) — a full compatibility matrix was considered and rejected for the README (see [`docs/ROADMAP.md`](ROADMAP.md), Phase 3) because it would need constant upkeep disproportionate to its value at this repository's size; individual recipe/doc pages can go deeper where it matters (e.g., language/framework is explicit per-entry already).

## Scoring dimensions (used in review, not published per-item)

When evaluating a PR, weigh:

- **Relevance** — how directly it supports building, testing, or connecting a closed-loop system
- **Real-time capability** — does it work online, or only offline?
- **Maturity & maintenance** — commit activity, release history, issue responsiveness
- **Documentation quality** — can a newcomer actually use it from the README/docs alone?
- **Reproducibility** — for papers/decoders, is there code + (ideally) data to reproduce results?
- **Openness** — license, and whether core functionality is usable without payment
- **Engineering usefulness** — does it save real implementation time, or is it a toy example?
- **Independent verifiability** — can the claimed capabilities be checked against something other than the project's own marketing copy (citations, independent users, a peer-reviewed paper, dependents)?

We do not publish numeric scores in the README. This rubric is for maintainer judgment during review.

## Category-specific policy

**Papers.** Only included if (a) they introduce a resource/method genuinely new to the field, and (b) they link a code repository. Prefer linking the code repo directly, with the paper as context.

**Datasets.** Must be openly downloadable (no gated access beyond standard data-use agreements) and, ideally, BIDS- or NWB-formatted (see [`docs/data/README.md`](data/README.md) for which standard fits which modality). Prefer meta-lists over cataloguing individual datasets, unless a dataset is a de facto closed-loop benchmark (e.g., LINK for cross-session stability).

**Commercial products/companies.** Allowed only if there's public technical documentation (an SDK, an API reference, a whitepaper with real specs) sufficient for an engineer to evaluate feasibility without contacting sales. No product pages with marketing copy only.

**Simulators.** Held to the same evidence bar as any tool, plus one more: the simulator's own documentation must be honest about what it doesn't capture (e.g., `bcisimulator`'s explicit disclosure that it lacks real physiological trial-timing variance). A simulator that oversells its own fidelity is disqualifying, not a minor flaw — see "Unverifiable projects" below.

**Connectors/interoperability resources.** A resource that bridges two layers (e.g., ROS-Neuro bridging neural acquisition and ROS) is evaluated against the layer it most substantively adds value to, and cross-referenced from [`docs/interoperability/README.md`](interoperability/README.md) rather than only living in one README section.

**Inactive repositories.** Not removed just because they're quiet — some closed-loop tools are simply *done*. Removal criteria: (a) no commits in 3+ years **and** (b) known-broken installation/build, **and** (c) an actively maintained equivalent now exists. Otherwise, flag as `maintenance: low`.

**Duplicate resources.** Keep the one with better documentation, more real-world adoption (citations, dependents, stars as a weak signal only), and clearer real-time story. Mention the alternative in the description if genuinely comparable, rather than listing both.

**Broken links.** Checked automatically by CI (see [`.github/workflows/link-check.yml`](../.github/workflows/link-check.yml)). A failed check should be reported through an issue or pull request so it can be investigated and corrected. Resources are not removed automatically.

**Unverifiable / low-quality / controversial projects.** If a project's claims can't be verified — implausibly specific, unbenchmarked feature claims; marketing-heavy documentation; no independent citations, adopters, or track record — it is held out **regardless of how well-written its documentation is.** This has come up concretely during this repository's own research: a couple of candidate resources were excluded from the MVP and this expansion specifically because their documentation read as promotional rather than technical, with precise-sounding but unverifiable claims. Documentation polish is not evidence of real-world use. When in doubt, track the candidate in an issue rather than merging it, and ask the contributor for independent evidence (a citation, an issue thread from another user, a dependent project).

## How new categories or docs pages get added

A new top-level README category is added only when at least ~4–5 qualifying resources exist for it *and* it doesn't fit inside an existing category without straining that category's coherence. A new `docs/` page (in the style of `engineering-notes/`) is justified when a topic needs conceptual explanation that a resource list format can't convey — as happened with Control & Adaptation, where the honest state of the field is "mostly bespoke code and papers, not packaged tools," which a link list would misrepresent. Until either bar is met, borderline material goes in the closest existing location with a clarifying note, or in an issue tagged `future-category`.
