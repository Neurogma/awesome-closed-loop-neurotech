# Safety, Security & Reliability

This is the section of the repository we handle most conservatively. The goal is to point engineers toward established, citable resources on real engineering concerns — not to speculate about hypothetical threats, and not to provide anything resembling medical or stimulation-safety instructions. If you are working on a system that stimulates human tissue, that work must happen under appropriate clinical, ethical, and regulatory oversight — nothing in this repository is a substitute for that.

## What's established vs. still emerging

| Topic | Status | Notes |
|---|---|---|
| Fail-safe / safe-state design | Established engineering practice, applied to this domain | See [Timing & Reliability](timing-and-reliability.md) — falling back to a known-safe state when closed-loop logic is unavailable is a stated design goal of real platforms like Dareplane (Recipe 2), not a novel idea |
| Stream/hardware health monitoring | Established practice | Detecting disconnected sensors, dropped packets, and out-of-range values before they reach a decoder or actuator is standard real-time systems engineering, applied here |
| Decoder confidence / uncertainty awareness | Emerging practice, inconsistent adoption | Not all decoders in this repository expose or use a confidence signal — this is a design choice worth making deliberately (see [Control & Adaptation](control-and-adaptation.md)) rather than an assumed default |
| Neurosecurity (adversarial attacks on neural decoders, privacy of neural data) | Active academic research area, not yet a mature engineering discipline with standard tooling | Treat claims in this space as research findings to be aware of, not settled best practice with off-the-shelf mitigations |
| Regulatory pathways for closed-loop neurostimulation devices | Established but jurisdiction-specific (e.g., FDA in the US) | Out of scope for this repository to summarize accurately and stay current — consult the relevant regulatory body's own guidance directly for anything beyond research use |

## Fail-safe design, concretely

Every recipe involving stimulation or robotic actuation (Recipes 2, 4, 6) inherits the same basic requirement: the system needs a defined behavior for when something goes wrong — a sensor disconnects, the decoder's confidence drops, the processing pipeline stalls, or a network link to a stimulator drops. "Falls back to off" is not always the right safe state (e.g., abruptly stopping DBS stimulation is not necessarily benign), which is precisely why this is a clinical/engineering co-design problem, not a pure software problem. This repository will not attempt to define what safe states are appropriate for a given stimulation protocol — that determination belongs with the clinical/research team responsible for the specific system.

## Neural data privacy

Neural signals, especially from wearable/consumer EEG devices, are increasingly recognized as sensitive personal data — several jurisdictions have begun extending data-privacy frameworks to explicitly cover neural data. This is a fast-moving legal/policy area; if you're building a system that collects neural data from real users, treat this as a topic requiring current legal guidance specific to your jurisdiction, not something this repository can authoritatively summarize.

## What we deliberately exclude from this section

- Specific stimulation parameters, dosing, or protocols of any kind — that is clinical/regulated content, not open-source engineering reference material, and providing it here would be irresponsible regardless of framing.
- Speculative "what if a hacker controlled someone's brain implant" content not grounded in a citable source — this makes for compelling writing and bad engineering guidance.
- Any resource whose primary content is alarmist rather than technical.

If you are aware of a well-established, citable resource on neurosecurity or closed-loop system safety that belongs here, see [`CONTRIBUTING.md`](../../CONTRIBUTING.md) — this section is intentionally thin today and should grow carefully, source by source.
