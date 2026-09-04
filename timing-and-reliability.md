# Timing & Reliability

Closed-loop neurotechnology is fundamentally a real-time systems engineering problem before it is a neuroscience problem. This note covers the concepts every recipe in this repository depends on, without inventing numbers we can't back with a source.

## The vocabulary, precisely

- **Acquisition latency** — time from a physical event in the brain/body to a digitized sample being available in software. Set by hardware (amplifier bandwidth, ADC conversion time) and driver buffering.
- **Processing latency** — time to filter, clean, and extract features from a buffer of samples. Scales with window length and algorithm complexity.
- **Inference latency** — time for the decoder (classical ML or DL) to produce an output from extracted features. Usually the smallest term for classical models, can dominate for large deep-learning decoders unless optimized.
- **Actuator latency** — time from a control decision to physical action (a robot moving, a stimulation pulse firing).
- **End-to-end latency** — the sum of all of the above, which is what actually determines whether a closed loop "feels" responsive or is fast enough to be safe/effective.
- **Window length** — how much data the decoder needs before it can produce a decision. This is often the single biggest lever on end-to-end latency, and it directly trades against decoding accuracy (see each recipe's "latency bottleneck" section).
- **Buffering** — data held in memory between stages to smooth out irregular arrival times; adds latency in proportion to buffer depth.
- **Jitter** — variation in latency from one loop iteration to the next. A system with low *average* latency but high jitter can still be unusable for applications needing consistent timing (e.g., stimulation locked to a specific oscillation phase).
- **Clock drift / synchronization error** — when two devices' clocks disagree, and by how much that disagreement grows over time. This is precisely the problem [LSL](https://github.com/sccn/labstreaminglayer)'s built-in time-synchronization (modeled after NTP) is designed to solve for multi-device setups — see the LSL paper (Kothe et al., *Imaging Neuroscience*, 2025) for the actual mechanism.

## Where these show up in practice

- **BCI cursor/motor control** (Recipe 1): window length is the dominant lever; shortening it directly trades accuracy for responsiveness.
- **Adaptive DBS** (Recipe 2): Dareplane's published benchtop evaluation explicitly measured per-stage and full-closed-loop latency across three different hardware configurations — this is the most rigorous public latency characterization referenced in this repository, and it's worth reading directly rather than summarizing into a single number here, because the numbers vary meaningfully by hardware configuration.
- **ROS-integrated systems** (Recipe 6): adds ROS message-passing latency and jitter on top of the neural decoding latency — usually small relative to decoding, but measurable and worth checking rather than assuming.

## We deliberately do not publish a "typical latency budget" table here

Every serious source we reviewed reports latency as specific to their hardware, algorithm, and application — there is no single trustworthy cross-application number, and publishing one anyway would misinform engineers making real design decisions. If you need a number, go to the specific recipe and the paper/repository it cites, and either read their measured latency or measure your own setup.

## How to actually measure your own system

At minimum:
1. Timestamp at acquisition (ideally hardware/driver-level, not application-level).
2. Timestamp at each processing stage boundary.
3. Timestamp at the actuator/output.
4. Report the full distribution (not just the mean) — jitter matters as much as average latency for most closed-loop applications, and a mean-only number hides tail-latency problems that can matter more than the average for safety-relevant systems.

LSL's built-in timestamping (see [`docs/interoperability/README.md`](../interoperability/README.md)) is the standard mechanism for step 1–2 across most tools in this repository's stack.

## Fail-safe design

A closed loop that silently keeps running on bad data (a disconnected sensor, a decoder producing garbage, a stalled processing pipeline) is worse than one that visibly stops. Concretely, this means:
- Defining an explicit "safe state" the system falls back to (e.g., Dareplane's design goal of falling back to known-safe open-loop stimulation if closed-loop decision logic becomes unavailable — see Recipe 2).
- Monitoring stream health (is data still arriving, at the expected rate, with plausible values) as a first-class part of the system, not an afterthought.
- Treating decoder confidence/uncertainty as a signal the control layer can act on, not just log — see [Control & Adaptation](control-and-adaptation.md) for where uncertainty-aware control fits.

See [Safety, Security & Reliability](safety-and-security.md) for the broader treatment of this topic.
