# Interoperability: How Do I Connect X to Y?

Most of what makes a closed-loop system hard to build isn't any single component — it's the seams between components. This page makes those seams explicit. If you're asking "how do I get data from A into B," this is the page to check before writing custom glue code.

## Hardware ↔ Acquisition

**The bridge:** device-specific SDKs (vendor-provided) or [BrainFlow](https://github.com/brainflow-dev/brainflow), which wraps many vendors' SDKs behind one unified API.

**What actually goes wrong here:** driver/firmware quirks per device (sampling rate not quite matching spec, timestamp jitter at the hardware level) — this is the layer with the least standardization in the entire stack, which is precisely why BrainFlow's value proposition (one API, many boards) matters as much as it does.

## Acquisition ↔ Streaming (LSL)

**The bridge:** most acquisition tools either natively output to LSL or have a documented LSL bridge — BrainFlow includes LSL streaming support; ROS-Neuro has a dedicated [`rosneuro_acquisition_lsl`](https://github.com/rosneuro/rosneuro_acquisition_lsl) plugin.

**What actually goes wrong here:** assuming LSL's clock-sync guarantees apply to *all* connected streams equally — they apply to streams within the LSL ecosystem, but a device with its own proprietary clock (common in clinical/implantable hardware, see Recipe 2) needs separate, often bespoke, synchronization work to align with LSL-native streams. See [Timing & Reliability](../engineering-notes/timing-and-reliability.md).

## Streaming ↔ Processing (LSL ↔ MNE)

**The bridge:** [MNE-LSL](https://github.com/mne-tools/mne-lsl), purpose-built to consume LSL streams directly into MNE-Python's real-time analysis objects.

**What actually goes wrong here:** buffer management — how much historical data MNE-LSL keeps in its ring buffer directly trades against memory use and the maximum window length your downstream decoder can request. This is a parameter worth setting deliberately, not leaving at a default.

## Processing/Decoding ↔ Machine Learning frameworks

**The bridge:** none of the tools in this repository impose a specific ML framework — [EEGNet](https://github.com/vlawhern/arl-eegmodels) is TensorFlow-based, `speechBCI`/`nejm-brain-to-text` are PyTorch-based. The processing layer's job is to hand off a clean feature array (typically a NumPy array); what happens after that is standard ML engineering.

**What actually goes wrong here:** feature array shape/ordering mismatches between what your processing pipeline emits and what your model expects — a mundane but extremely common source of silent bugs, worth writing an explicit shape-check for at this boundary.

## Decoder ↔ ROS2 / Robotics

**The bridge:** [ROS-Neuro](https://github.com/rosneuro) if your acquisition/processing is also happening inside the ROS graph; otherwise, publish your decoder's output as a plain ROS topic from whatever process is running it (Python's `rclpy` or C++'s `rclcpp`) — this is the more common pattern when your decoding stack (e.g., EEGNet in TensorFlow) has no reason to live inside ROS itself.

**What actually goes wrong here:** message-passing latency and queue depth defaults in ROS can silently add delay if a decoder publishes faster than a downstream control node consumes — worth checking explicitly for anything latency-sensitive (Recipe 6).

## Decoder ↔ Stimulation hardware

**The bridge:** this is the least standardized connector in the entire stack. [Dareplane](https://github.com/bsdlab/Dareplane) is explicitly designed to solve this generically (technology-agnostic orchestration over TCP between a decision module and a stimulator module) — it's the strongest general-purpose answer we found. Outside Dareplane, this is typically bespoke, vendor-specific integration work (see Recipe 2).

**What actually goes wrong here:** stimulator command latency and command-acknowledgment handling — does your system know a stimulation command was actually received and executed, or does it just assume so? This matters enormously for the fail-safe design discussed in [Timing & Reliability](../engineering-notes/timing-and-reliability.md).

## Experiment ↔ Data standard (BIDS/NWB)

**The bridge:** most acquisition/analysis tools in this stack (MNE-Python especially) have built-in BIDS I/O support (`mne-bids`); NWB has PyNWB/MatNWB for direct read/write, plus a growing set of format-specific conversion scripts (see [Datasets](../data/README.md)).

**What actually goes wrong here:** metadata gets lost or under-specified at the point of recording if you're not already recording in a BIDS/NWB-aware way — retrofitting proper metadata onto already-collected data is much more painful than recording it correctly the first time. Decide your target standard before you start collecting, not after.

## Simulation ↔ Real hardware

**The bridge:** the practical pattern is to make your simulated data source indistinguishable from a real one at the interface boundary — e.g., publishing simulated data over the same LSL stream a real device would use, so the rest of your pipeline doesn't know the difference. See [Simulation & Hardware-in-the-Loop](../simulation/README.md).

**What actually goes wrong here:** a simulator that's convenient to swap out is only useful if it was built with that swap in mind from the start — retrofitting a hardware abstraction after the fact is real engineering work, so if you plan to test in simulation before real hardware, design the interface boundary early.

---

## The pattern across all of these

Nearly every "what goes wrong" above is a variant of the same problem: **an interface that looks compatible on paper (same data type, similar shape) but carries an implicit assumption (about timing, ordering, units, or acknowledgment) that isn't checked automatically.** This is why [Timing & Reliability](../engineering-notes/timing-and-reliability.md)'s emphasis on explicit measurement, and [Safety, Security & Reliability](../engineering-notes/safety-and-security.md)'s emphasis on stream health monitoring, matter as much at these connector boundaries as anywhere else in the stack.
