# Roadmap

## Where we are now (post-expansion)

The repository has moved from a ~30-resource curated list to a structured engineering knowledge base: 7 README building-block sections, 6 fully-specified system recipes with honest maturity labels, and 6 conceptual engineering-notes pages (timing, control, benchmarking, safety, what-to-use, open questions), plus dedicated interoperability and simulation pages. The core discipline — every entry earns its place against `docs/CRITERIA.md`, nothing is invented — was maintained throughout this expansion; several candidate resources and tools were researched and explicitly excluded for failing the verifiability bar rather than included for volume.

## Phase 1 — Hardening (0–3 months from this expansion)
- Fix broken links / bad descriptions surfaced by early users.
- Verify current maintenance status of the thinner-activity resources (ROS-Neuro packages, Dareplane) as the ecosystem moves.
- Recruit 1–2 co-maintainers with real closed-loop engineering or research background — a solo-maintained repository at this depth is a bus-factor risk.
- Add a `CHANGELOG.md` once content changes meaningfully post-launch.
- **Do not** add new top-level README categories yet unless a gap is reported repeatedly by users, not just identified internally.

## Phase 2 — Community (3–9 months)
- Open contributions fully once `CRITERIA.md` and the recipe template have been stress-tested by real external PRs.
- Revisit the **tiering question** (Core/Advanced/Emerging) raised during this expansion: we did not introduce formal tiers in this pass because the current resource count per section (4–6 each) doesn't yet strain a flat list, and a tier label decoration-only would violate this repository's own "don't use tiers merely for decoration" principle. Revisit once any section exceeds ~10 entries.
- Fill genuinely identified gaps if and when a qualifying resource appears:
  - A general-purpose, actively-maintained synthetic EEG/neural signal generator (see [`docs/simulation/README.md`](simulation/README.md) — none met the bar at expansion time)
  - An RL environment standardized for closed-loop BCI control research (see [Open Questions](engineering-notes/open-questions.md))
  - A packaged, reusable Kalman/MPC control library for BCI specifically (currently bespoke per-lab code — see [Control & Adaptation](engineering-notes/control-and-adaptation.md))

## Phase 3 — Structured knowledge base (9–18 months, conditional)
- Only if the docs/ tree becomes genuinely hard to navigate as flat markdown: consider a lightweight static site (GitHub Pages + a simple generator) rendering the same source-of-truth markdown into a filterable table (by modality, invasiveness, real-time capability, resource type, license).
- Any such site must generate from the markdown files, not fork them — single source of truth, always.
- **Revisit the compatibility-metadata question explicitly deferred in `docs/CRITERIA.md`:** a full compatibility matrix (OS × language × hardware × modality) was considered during this expansion and rejected for the README as disproportionate upkeep at current scale. If the repository reaches a size where this becomes genuinely useful rather than decorative, it belongs in a generated docs page, not hand-maintained markdown.

## Phase 4 — Possible directory / ecosystem map (18+ months, speculative)
- A searchable "neurotech stack explorer" — filter by layer, modality, license, latency class — is plausible *if* the resource count and community both justify it.
- A dependency/relationship graph between platforms (e.g., "Dareplane can orchestrate py_neuromodulation," "ROS-Neuro can consume LSL streams") is an interesting long-term idea, made more concrete by this expansion's interoperability page — but explicitly **not** something to build until Phase 2 community exists to maintain it.

## Explicitly not building yet
- A website (Phase 3 is conditional, not committed)
- A scoring/ranking algorithm for resources
- Auto-generated content from scraping GitHub topics (defeats the purpose of curation)
- A full compatibility matrix (see Phase 3)
- A Slack/Discord community (point to NeuroTechX's existing community instead)
- Formal Core/Advanced/Emerging tiers (see Phase 2 — not yet earned)

---

## Research summary (why this repository exists, and why this expansion was scoped as it was)

The original validation (see the repository's initial research) found no existing "awesome closed-loop neurotechnology" repository and confirmed the gap was real: general BCI/neuroscience lists organize by domain, not by pipeline.

This expansion pass specifically investigated and resolved several concrete gaps: control theory tooling (found to be genuinely thin — mostly bespoke lab code, documented honestly as such rather than padded with tangential general-purpose libraries), simulation/HIL testing (found one purpose-built closed-loop simulator, `bcisimulator`, and documented the real gaps rather than inventing coverage), neuro-robotics connectors (found a genuine, peer-reviewed answer in ROS-Neuro), and benchmarking (found a strong, well-adopted answer in MOABB). Several candidate tools surfaced during this research were excluded for making unverifiable or promotional claims rather than demonstrating independent adoption — this is noted explicitly in `docs/CRITERIA.md` because it's a real, recurring judgment call, not a hypothetical policy.

This gap analysis and the resulting scope should be re-validated periodically (roughly annually, or whenever a maintainer suspects the ecosystem has shifted) — see the first checklist item in any future roadmap review.
