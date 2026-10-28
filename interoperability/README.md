# Interoperability: How Do I Connect X to Y?

Most of what makes a closed-loop system hard to build isn't any single component — it's the seams between components. This page makes those seams explicit. If you're asking "how do I get data from A into B," this is the page to check before writing custom glue code.

## Hardware ↔ Acquisition

**The bridge:** device-specific SDKs (vendor-provided) or [BrainFlow](https://github.com/brainflow-dev/brainflow), which wraps many vendors' SDKs behind one unified API.

**Common failure modes:** driver and firmware quirks can differ by device, including sampling rates that do not exactly match the specification and hardware-level timestamp jitter. This is the least standardized layer of the stack, which is one reason BrainFlow's single-API approach is useful.

## Acquisition ↔ Streaming (LSL)

**The bridge:** most acquisition tools either natively output to LSL or have a documented LSL bridge — BrainFlow includes LSL streaming support; ROS-Neuro has a dedicated [`rosneuro_acquisition_lsl`](https://github.com/rosneuro/rosneuro_acquisition_lsl) plugin.

**Common failure modes:** assuming LSL's clock synchronization applies equally to *all* connected streams. It applies to streams within the LSL ecosystem; a device with its own proprietary clock (common in clinical/implantable hardware, see Recipe 2) needs separate synchronization work, which is often bespoke. See [Timing & Reliability](../engineering-notes/timing-and-reliability.md).

## Streaming ↔ Processing (LSL ↔ MNE)

**The bridge:** [MNE-LSL](https://github.com/mne-tools/mne-lsl), purpose-built to consume LSL streams directly into MNE-Python's real-time analysis objects.

**Common failure modes:** buffer management — how much historical data MNE-LSL keeps in its ring buffer directly trades against memory use and the maximum window length your downstream decoder can request. This is a parameter worth setting deliberately, not leaving at a default.

## Processing/Decoding ↔ Machine Learning frameworks

**The bridge:** none of the tools in this repository impose a specific ML framework — [EEGNet](https://github.com/vlawhern/arl-eegmodels) is TensorFlow-based, `speechBCI`/`nejm-brain-to-text` are PyTorch-based. The processing layer's job is to hand off a clean feature array (typically a NumPy array); what happens after that is standard ML engineering.

**Common failure modes:** feature-array shape or ordering mismatches between the processing pipeline and the model are a common source of silent bugs. Add an explicit shape check at this boundary.

## Decoder ↔ ROS2 / Robotics

**The bridge:** [ROS-Neuro](https://github.com/rosneuro) if your acquisition/processing is also happening inside the ROS graph; otherwise, publish your decoder's output as a plain ROS topic from whatever process is running it (Python's `rclpy` or C++'s `rclcpp`) — this is the more common pattern when your decoding stack (e.g., EEGNet in TensorFlow) has no reason to live inside ROS itself.

**Common failure modes:** ROS message-passing latency and queue-depth defaults can add delay when a decoder publishes faster than a downstream control node consumes. Check these values for latency-sensitive systems (Recipe 6).

## Decoder ↔ Stimulation hardware

**The bridge:** this is the least standardized connector in the entire stack. [Dareplane](https://github.com/bsdlab/Dareplane) is explicitly designed to solve this generically (technology-agnostic orchestration over TCP between a decision module and a stimulator module) — it's the strongest general-purpose answer we found. Outside Dareplane, this is typically bespoke, vendor-specific integration work (see Recipe 2).

**Common failure modes:** stimulator command latency and command-acknowledgment handling. The system should distinguish a command being sent from a command being received and executed. This affects the fail-safe design discussed in [Timing & Reliability](../engineering-notes/timing-and-reliability.md).

## Experiment ↔ Data standard (BIDS/NWB)

**The bridge:** most acquisition/analysis tools in this stack (MNE-Python especially) have built-in BIDS I/O support (`mne-bids`); NWB has PyNWB/MatNWB for direct read/write, plus a growing set of format-specific conversion scripts (see [Datasets](../data/README.md)).

**Common failure modes:** metadata can be lost or underspecified at recording time if the acquisition process does not account for BIDS/NWB requirements. Retrofitting metadata later is harder than capturing it during acquisition, so choose the target standard before data collection.

## Simulation ↔ Real hardware

**The bridge:** the practical pattern is to make your simulated data source indistinguishable from a real one at the interface boundary — e.g., publishing simulated data over the same LSL stream a real device would use, so the rest of your pipeline doesn't know the difference. See [Simulation & Hardware-in-the-Loop](../simulation/README.md).

**Common failure modes:** a simulator is easier to replace when the interface is designed for it from the start. Retrofitting a hardware abstraction later adds engineering work, so define that boundary early when simulation is part of the test plan.

---

## The pattern across all of these

Nearly every "what goes wrong" above is a variant of the same problem: **an interface that looks compatible on paper (same data type, similar shape) but carries an implicit assumption (about timing, ordering, units, or acknowledgment) that isn't checked automatically.** This is why [Timing & Reliability](../engineering-notes/timing-and-reliability.md)'s emphasis on explicit measurement, and [Safety, Security & Reliability](../engineering-notes/safety-and-security.md)'s emphasis on stream health monitoring, matter as much at these connector boundaries as anywhere else in the stack.
