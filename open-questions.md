# Open Questions

This page exists to show where the closed-loop neurotechnology ecosystem is still technically immature — not to speculate wildly, but to give an honest map of what isn't solved yet. Each item below is grounded in a concrete gap surfaced during this repository's research, not a generic "AI safety" or "future of neurotech" musing.

## Cross-session and cross-subject decoder stability

Decoders trained on one session or one participant typically degrade when applied to a new session or a new person, for both non-invasive (electrode placement variance) and invasive (chronic tissue response, signal drift) systems. The [LINK dataset](../data/README.md) exists specifically because the field needed a standardized way to study this for intracortical BMIs — its existence is itself evidence of how unresolved the problem is. See [Control & Adaptation](control-and-adaptation.md).

## Calibration burden

Nearly every recipe in this repository requires participant-specific calibration before use, ranging from minutes (EMG threshold setting) to substantial multi-session data collection (speech neuroprosthesis training). Published work reducing this (e.g., 3-minute calibration-to-plateau with GP-DKF, cited in [Control & Adaptation](control-and-adaptation.md)) shows progress but is not yet the field's standard practice.

## Latency vs. accuracy as a first-class design trade-off

Every recipe in this repository trades decoding window length against accuracy, but this trade-off is rarely benchmarked systematically or reported as a curve (accuracy at multiple latency budgets) rather than a single operating point. This makes it hard to compare methods fairly on a latency-matched basis — see [Benchmarking & Validation](benchmarking.md).

## Synchronization across heterogeneous hardware

LSL solves time-synchronization well within its own ecosystem, but closed-loop systems combining, say, an implanted sensing device's proprietary clock with LSL-based peripherals (Recipe 2's exact scenario) still require careful, often bespoke, synchronization engineering — there isn't yet a universal solution once you leave the LSL-native tool ecosystem.

## Real-time artifact suppression without discarding signal

Artifact Subspace Reconstruction (ASR) and similar online-capable methods are effective but were largely developed for scalp EEG; robust, standardized real-time artifact handling for other modalities (ECoG, LFP) is comparatively less mature and often handled with ad hoc, lab-specific approaches.

## Online adaptation without catastrophic drift

Updating a decoder during live use (rather than only via offline recalibration) risks the decoder drifting away from good performance rather than toward it, especially compounded with co-adaptation effects (the user is also adapting). Robust online/continual learning for closed-loop neural decoders remains a genuinely open problem, not a solved-but-unpublicized one.

## Multimodal fusion

Several recipes in this repository use a single modality (EEG, or intracortical, or EMG) in isolation. Combining modalities (e.g., EEG + eye tracking, or EEG + EMG) for richer or more robust control is an active research direction without a standard, widely-adopted open-source fusion framework yet — this repository does not currently list one because we could not identify a resource meeting our inclusion bar for this specific gap.

## Uncertainty-aware control

Most control logic in this repository's recipes (thresholds, simple classifiers) does not explicitly propagate decoder uncertainty into the control decision. Doing so — having the controller behave more conservatively when the decoder is less confident — is conceptually straightforward but not standard practice across the tools surveyed here.

## Standardized real-time / closed-loop benchmarks

As covered in [Benchmarking & Validation](benchmarking.md), the field has reasonable standardized *offline* benchmarks (BCI Competition datasets, Brain-to-Text Competition) but comparatively little standardized *online, closed-loop* benchmarking — which is precisely the condition that matters most for the systems this repository is about.

## Open-hardware validation for stimulation-side systems

Acquisition-side open hardware (OpenBCI and similar) is mature and widely used. Open, accessible hardware for the stimulation/actuation side of a closed loop — particularly anything touching neurostimulation rather than robotics — is comparatively rare; the [open-source closed-loop DBS in rat](https://www.nature.com/articles/srep09921) is a rare fully-documented example, and it's an animal research system, not a human-ready reference design.

## Simulation-to-real transfer

Tools like [`jtcostello/bcisimulator`](https://github.com/jtcostello/bcisimulator) explicitly acknowledge that their simulated neural data does not capture real physiological complexity (e.g., trial-timing variance). How well a decoder validated in simulation transfers to real closed-loop performance is an open question the simulator's own documentation is honest about, not something this repository can resolve.

---

This list will be revisited as part of the roadmap's periodic re-validation (see [`docs/ROADMAP.md`](../ROADMAP.md)) — an "open question" that gets a solid, well-adopted open-source answer should graduate out of this page and into the main building blocks.
