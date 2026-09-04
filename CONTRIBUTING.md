# Contributing

Thanks for considering a contribution. This repository stays useful by staying small, curated, and specific to closed-loop neurotechnology engineering — please read [`docs/CRITERIA.md`](docs/CRITERIA.md) before opening a PR.

## Adding a resource to the README

1. **Check it isn't already here** (search the README first, including the linked `docs/` pages — some resources are cross-referenced rather than only living in one place).
2. **Check it passes the one-question test** in `docs/CRITERIA.md`.
3. **Pick the right section.** If nothing fits, open an issue first rather than forcing it in.
4. **Format:**
   ```markdown
   - [Name](URL) — one-sentence description explaining why it matters *for closed-loop work specifically*. `tag1` `tag2` `TYPE` · License/Language
   ```
   Required: a resource-type label from `docs/CRITERIA.md` (`TOOL`, `FRAMEWORK`, `HARDWARE`, `DATASET`, `STANDARD`, `PAPER+CODE`, `PROTOCOL`, `SIMULATOR`, `BENCHMARK`, `REFERENCE LIST`).
   Optional tags: `real-time-capable`, `invasive`, `non-invasive`, `research`, `clinical`. Use only tags that add real information.
5. **One resource per pull request.**
6. **Explain your reasoning in the PR description** — what layer (or connector) does this fill, and why is it better than or complementary to what's already listed?

## Adding or editing a system recipe

Recipes live in [`docs/systems/`](docs/systems/). Each must:
- Name specific, real tools already in the README (not placeholders)
- Answer all 15 questions in the [recipe template](docs/systems/README.md#every-recipe-answers-the-same-15-questions)
- Carry an accurate maturity label (**conceptual architecture** / **research prototype** / **experimentally demonstrated** / **clinically validated**) with a citable source for that label
- State the latency bottleneck and reliability concerns honestly, including "we don't know" where that's the truth
- Include the "what's realistically reproducible vs. what requires specialized/clinical infrastructure" section — this is not optional, it's the section most likely to prevent someone from wasting months on an infeasible plan

## Adding to an engineering note

The pages in [`docs/engineering-notes/`](docs/engineering-notes/) (timing, control, benchmarking, safety, what-to-use, open questions) are conceptual treatments, not resource lists. Contributions here should either (a) add a real, citable tool/paper where one now exists for a previously-uncovered gap, or (b) correct/update an existing claim with a better source. Speculative additions ("this could be a good idea") belong in an issue, not a PR to these pages.

## Reporting a broken link or outdated entry

Open an issue using the "Broken link / outdated entry" template, or submit a PR directly if you know the fix.

## Reporting an unverifiable or overclaiming resource already in the repo

If you find an entry whose claims don't hold up — the "Unverifiable projects" policy in `docs/CRITERIA.md` — open an issue with what you found. This repository has already excluded candidates for this reason during its own research and treats it seriously.

## What reviewers check

- Does it satisfy every item in `docs/CRITERIA.md`?
- Is the description accurate and non-promotional?
- Is it in the right place — README section, or one of the `docs/` pages?
- Is there evidence of real maintenance/adoption (not just that it exists)?
- For recipes: is the maturity label actually supported by the cited source?

We may ask for evidence (a citation, a changelog, an adoption signal) before merging, especially for newer or less-established projects. This isn't personal — it's the same bar every entry here is held to.

## Code of Conduct

Participation is governed by our [Code of Conduct](CODE_OF_CONDUCT.md).
