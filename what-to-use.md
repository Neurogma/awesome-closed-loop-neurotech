# What to Use

People don't only ask "what exists?" — they ask "what should I use?" This page gives evidence-based defaults, not rankings. There is no universally best tool; every recommendation below is qualified by the situation it fits.

## "I want the fastest path to a working non-invasive BCI prototype"

**Good default:** OpenBCI hardware → [BrainFlow](https://github.com/brainflow-dev/brainflow) → [LSL](https://github.com/sccn/labstreaminglayer) → [MNE-LSL](https://github.com/mne-tools/mne-lsl) → CSP+LDA or [EEGNet](https://github.com/vlawhern/arl-eegmodels).

**Why:** every link in this chain is open-source, well-documented, and Python-first, so you can go from unboxing hardware to a working online pipeline without writing acquisition or streaming code from scratch. See [Recipe 1](../systems/eeg-motor-bci.md) for the full walkthrough.

**Trade-off:** you're accepting whatever latency and channel-count limits your consumer EEG hardware imposes — this is a prototyping stack, not a research-grade high-density EEG setup.

## "I want a Python-first real-time stack"

**Strong choice:** [Dareplane](https://github.com/bsdlab/Dareplane) if your work is closed-loop and technology-agnostic (works across invasive and non-invasive modalities); [MNE-LSL](https://github.com/mne-tools/mne-lsl) + [py_neuromodulation](https://github.com/neuromodulation/py_neuromodulation) if you specifically need real-time intracranial feature extraction.

**Trade-off:** Dareplane is younger (2025 publication) than BCI2000/OpenViBE — weigh community size and track record against its purpose-built closed-loop design and technology-agnostic architecture.

## "I want an open-hardware stack, not just open-source software"

**Good default:** OpenBCI (hardware) end-to-end with BrainFlow/LSL/MNE. For stimulation-side open hardware, the [open-source closed-loop DBS in rat](https://www.nature.com/articles/srep09921) is the clearest fully-documented example in this repository, though it's a research/animal system, not something to adapt directly for human use.

**Trade-off:** open hardware generally means more integration work and less regulatory/clinical validation than commercial research-grade systems (e.g., g.tec, ANT Neuro, etc.).

## "I want a research-grade, clinically-oriented stack"

**Strong choice when:** you have access to clinical-grade acquisition hardware and need a platform with a long clinical/academic track record — [BCI2000](https://www.bci2000.org/) or [OpenViBE](https://openvibe.inria.fr/).

**Trade-off:** both are C++-based with less modern tooling ergonomics than the Python-first stacks above; the trade is maturity and track record for development speed.

## "I need the lowest possible latency"

**What actually determines this:** for non-invasive BCI, your window length dominates far more than your software stack choice (see [Timing & Reliability](timing-and-reliability.md)) — optimizing software before shortening your decoding window is usually the wrong place to start. For adaptive DBS specifically, Dareplane's published benchtop latency measurements across three hardware configurations are the most rigorous public reference point available — read the actual numbers in the paper rather than assuming a stack choice alone determines latency.

**We will not** recommend a stack as "the fastest" without a source, because latency is dominated by your specific hardware and windowing choices, not by the framework alone.

## "I want to integrate with robotics"

**Good default:** [ROS-Neuro](https://github.com/rosneuro) if you want neural acquisition/preprocessing exposed as native ROS topics/nodes; otherwise, decode with any stack above and publish the result to a ROS topic yourself.

**Trade-off:** ROS-Neuro's own package activity is modest — treat it as a solid academic reference implementation to build on or learn from, not a large, actively-growing open-source community project. See [Recipe 6](../systems/neuro-robotics-ros.md).

## "I want to test before touching a real subject"

**Good default:** [`jtcostello/bcisimulator`](https://github.com/jtcostello/bcisimulator) for closed-loop decoder testing against synthetic neural data; a replayed/simulated LSL stream for testing your acquisition-through-processing pipeline without live hardware. See [Simulation & Hardware-in-the-Loop](../simulation/README.md).

## "I need clinical-grade adaptive stimulation infrastructure"

**Strong choice when:** you have clinical/research access to implanted or externalized hardware and IRB oversight — [Dareplane](https://github.com/bsdlab/Dareplane) (published in-human closed-loop validation) or [CLoSES-RT](https://github.com/Center-For-Neurotechnology/CLoSES-RT) (Simulink Real-Time, from the Center for Neurotechnology).

**Trade-off:** CLoSES-RT's MATLAB/Simulink base fits teams already standardized on that toolchain; Dareplane's Python base fits teams that aren't.

---

None of these recommendations are static facts — they reflect the state of the ecosystem as researched for this repository (see [`docs/ROADMAP.md`](../ROADMAP.md) for the re-validation cadence). If your situation doesn't match one of the above, the [Building Blocks](../../README.md#building-blocks) table in the main README is the right starting point for assembling your own combination.
