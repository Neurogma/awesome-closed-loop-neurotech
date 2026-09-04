# Awesome Closed-Loop Neurotechnology [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> An engineering map — not a link dump — for building **closed-loop neurotechnology systems**: sensing, real-time processing, decoding, control, and stimulation, in a continuous feedback loop with the brain or body.

```
sensing → acquisition/sync → signal processing → decoding → decision/control → stimulation/actuation → feedback → (loop)
```

This maps **how the pieces fit together and what connects them** — not just what exists. If you're asking *"what do I use, and how does it talk to the thing next to it?"* — start here.

---

## Why this exists, and what it isn't

Good BCI and neuroscience resource lists already exist ([`NeuroTechX/awesome-bci`](https://github.com/NeuroTechX/awesome-bci), [`awesome-neuroscience`](https://github.com/analyticalmonk/awesome-neuroscience)). None is organized around the constraint that defines closed-loop systems: **the loop has to run, end-to-end, inside a latency budget, and stay running.** That constraint is what determines which tools matter and how they connect — it's the organizing principle here.

**This repository is not:**
- A general neuroscience or BCI reading list (see the two above)
- An exhaustive catalog — see [`docs/CRITERIA.md`](docs/CRITERIA.md) for what's deliberately left out
- A benchmark authority — see [`docs/engineering-notes/benchmarking.md`](docs/engineering-notes/benchmarking.md) for why we report only what's been independently verified

**It is:** a curated map of building blocks, the connectors between them, worked reference architectures with honest bottlenecks, and the open engineering problems the field hasn't solved yet.

---

## Start Here

**I'm an engineer building a real-time BCI.**
[The Stack](#the-neurotech-stack) → [Building Blocks](#building-blocks) → pick a [Full-Stack Platform](#6-full-stack-platforms) → [System Recipes](docs/systems/README.md) for the closest match to your use case → [Timing & Reliability](docs/engineering-notes/timing-and-reliability.md) before you trust your latency numbers.

**I'm a researcher prototyping a closed-loop experiment (neurofeedback, aDBS).**
[Full-Stack Platforms](#6-full-stack-platforms) comparison → [Datasets & Standards](#7-datasets-standards) for BIDS/NWB-compliant data handling → [Simulation & Hardware-in-the-Loop](docs/simulation/README.md) to validate before touching a subject.

**I'm a robotics/control engineer new to neurotech.**
[The Neurotech Stack](#the-neurotech-stack) diagram → [Control & Adaptation](docs/engineering-notes/control-and-adaptation.md) — this is where your existing skills map directly onto this field → [Interoperability](docs/interoperability/README.md) for how decoders talk to ROS.

**I'm new to the field entirely.**
[The Neurotech Stack](#the-neurotech-stack) → [Recipe 1: EEG → cursor](docs/systems/eeg-motor-bci.md), the most reproducible end-to-end recipe here.

---

## The Neurotech Stack

```
HARDWARE (electrodes, amplifiers, stimulators, actuators)
    │
    ▼
┌───────────────────────┐
│ ACQUISITION            │  device drivers, ADCs → digital samples
└───────────────────────┘
    │
    ▼
┌───────────────────────┐
│ SYNCHRONIZATION        │  streaming, time-sync, multi-modal alignment, event markers
└───────────────────────┘
    │
    ▼
┌───────────────────────┐
│ SIGNAL PROCESSING      │  filtering, online artifact removal, feature extraction
└───────────────────────┘
    │
    ▼
┌───────────────────────┐
│ DECODING               │  classical ML → DL → foundation models → language models
└───────────────────────┘
    │
    ▼
┌───────────────────────┐
│ DECISION / CONTROL     │  thresholds, state machines, Kalman/MPC, RL, shared autonomy
└───────────────────────┘
    │
    ▼
┌───────────────────────┐
│ STIMULATION/ACTUATION  │  DBS pulse, neurofeedback cue, robotic actuator, prosthesis
└───────────────────────┘
    │
    ▼
FEEDBACK ──────────► back to hardware / brain / body
```

**Latency-sensitive boundary:** Signal Processing through Stimulation typically must complete within a fixed budget — tighter for adaptive DBS and intracortical control than for neurofeedback. See [`docs/engineering-notes/timing-and-reliability.md`](docs/engineering-notes/timing-and-reliability.md) for what determines that budget and how to measure it, rather than assume it.

**Why the taxonomy is hierarchical here and flat there:** Acquisition/Sync/Processing/Decoding/Control are hierarchical because a resource genuinely lives in one layer. Full-Stack Platforms is flat and separate because those tools *span* layers — forcing them into one layer would misrepresent them. Connectors (below) are their own category because a resource that bridges two layers isn't "in" either one.

---

## Building Blocks

The stack above tells you the order. This tells you what fills each slot, and points to where the real choices and trade-offs live.

| Layer | What it does | Where the depth lives |
|---|---|---|
| **Acquisition** | Get raw signal off hardware into software | [§1](#1-acquisition-sensing) below |
| **Synchronization** | Align samples across devices/modalities in time | [§2](#2-streaming-synchronization) below |
| **Signal processing** | Filter, remove artifacts, extract features online | [§3](#3-signal-processing-artifact-removal) below |
| **Decoding** | Turn features into the decoded variable | [§4](#4-decoding-representation) below |
| **Control** | Turn a decoded state into an action | [`docs/engineering-notes/control-and-adaptation.md`](docs/engineering-notes/control-and-adaptation.md) |
| **Stimulation / actuation** | Close the loop physically | [§5](#5-stimulation-actuation) below |
| **Connectors** | Bridge two layers together | [`docs/interoperability/README.md`](docs/interoperability/README.md) |
| **Simulation / HIL** | Test before touching a subject | [`docs/simulation/README.md`](docs/simulation/README.md) |

---

## 1. Acquisition & Sensing

- [BrainFlow](https://github.com/brainflow-dev/brainflow) — Board-agnostic C++/Python/Java/etc. SDK for EEG/EMG/ECG biosensors (OpenBCI, g.tec Unicorn, BrainBit, and more); one API regardless of hardware, with built-in signal-processing utilities. `real-time-capable` `non-invasive` `TOOL` · MIT
- [OpenBCI](https://github.com/OpenBCI) — Open-source EEG/EMG/ECG hardware (Cyton, Ganglion) and GUI; the most common entry point for DIY closed-loop prototyping. `non-invasive` `HARDWARE`
- [I-Spin live](https://github.com/simonavrillon/I-Spin) — Open-source, peer-reviewed real-time blind-source-separation software decoding individual spinal motor neuron firing activity from surface/intramuscular EMG. `real-time-capable` `non-invasive` `TOOL`

**Modalities in scope, briefly:** EEG (scalp, non-invasive), ECoG (subdural, invasive), intracortical/single-unit (penetrating arrays, invasive), LFP (local field potential, typically from DBS/sensing electrodes), EMG (peripheral muscle), fNIRS (optical, non-invasive, slower dynamics). Each has different sampling rates, channel counts, and noise profiles that ripple through every downstream layer — see the relevant recipe in [`docs/systems/`](docs/systems/README.md) for modality-specific handling.

## 2. Streaming & Synchronization

- [Lab Streaming Layer (LSL)](https://github.com/sccn/labstreaminglayer) — The de facto standard for unified, time-synchronized streaming of neural, physiological, and behavioral data across a local network. `real-time-capable` `PROTOCOL` · C/C++/Python/MATLAB/Java/C# · MIT
- [MNE-LSL](https://github.com/mne-tools/mne-lsl) — Real-time brain-signal streaming framework binding LSL into MNE-Python for online analysis. `real-time-capable` `TOOL` · Python · BSD-3-Clause
- [XDF (Extensible Data Format)](https://github.com/sccn/xdf) — Multi-stream container file format LSL recordings are saved to. `STANDARD`

See [`docs/interoperability/README.md`](docs/interoperability/README.md) for how synchronization actually fails in practice (clock drift, jitter, dropped samples) and what to check for.

## 3. Signal Processing & Artifact Removal

- [MNE-Python](https://github.com/mne-tools/mne-python) — Standard Python library for M/EEG/iEEG/ECoG processing and filtering; the base most real-time tools above build on. `research` `TOOL` · BSD-3-Clause
- [EEGLAB `clean_rawdata` (ASR)](https://github.com/sccn/clean_rawdata) — Reference implementation of Artifact Subspace Reconstruction, the standard online-capable method for removing large-amplitude EEG artifacts in real time. `real-time-capable` `TOOL` · MATLAB
- [ICLabel](https://github.com/sccn/ICLabel) — Automatic classifier for ICA components (brain vs. eye/muscle/heart/line-noise/channel-noise). `TOOL` · MATLAB/Python
- [py_neuromodulation](https://github.com/neuromodulation/py_neuromodulation) — Real-time feature-extraction library for intracranial neurophysiology (LFP/ECoG), built for closed-loop neuromodulation pipelines; JOSS-published. `real-time-capable` `invasive` `TOOL` · Python · MIT

## 4. Decoding & Representation

- [EEGNet](https://github.com/vlawhern/arl-eegmodels) — Compact CNN architecture for EEG-based BCI decoding; the most widely reused baseline decoder in the field. `TOOL` · Python/TensorFlow
- [speechBCI](https://github.com/fwillett/speechBCI) — Reference implementation of the RNN decoder behind *"A high-performance speech neuroprosthesis"* (Nature, 2023): intracortical spikes → phonemes → text. `invasive` `real-time-capable` `PAPER+CODE` · Python
- [nejm-brain-to-text](https://github.com/Neuroprosthetics-Lab/nejm-brain-to-text) — Full training/eval pipeline for the rapid-calibration speech neuroprosthesis (NEJM, 2024), including language-model decoding. `invasive` `PAPER+CODE` · Python
- [awesome-neurofm](https://github.com/mazabou/awesome-neurofm) — Curated foundation models for neural population activity (POYO, NDT2, and others). `REFERENCE LIST`
- [awesome-brain-decoding](https://github.com/NeuSpeech/awesome-brain-decoding) — Curated papers-with-code for brain decoding across modalities. `REFERENCE LIST`
- [MOABB](https://github.com/NeuroTechX/moabb) — NeuroTechX-backed reproducible benchmarking framework: one API across dozens of public EEG datasets with built-in within-session, cross-session, and cross-subject evaluation. The strongest standardized benchmark resource in this field. `BENCHMARK` `TOOL` · Python · BSD

The decision logic that sits between a decoded variable and an actuator command — thresholds, Kalman filters, MPC, RL, shared autonomy — is covered as a coherent topic in [`docs/engineering-notes/control-and-adaptation.md`](docs/engineering-notes/control-and-adaptation.md) rather than as a resource list, because the open-source tooling here is mostly bespoke lab code, not packaged libraries — the honest thing is to explain the concepts and point to the papers that established them.

## 5. Stimulation & Actuation

- [py_neuromodulation](https://github.com/neuromodulation/py_neuromodulation) *(cross-listed from §3)* — Also provides the real-time feature stream that adaptive-stimulation decision logic consumes.
- [Open-source closed-loop DBS in rat (Nature Sci. Rep.)](https://www.nature.com/articles/srep09921) — Fully documented open-hardware closed-loop DBS system; the clearest public worked example of a complete sense→decide→stimulate loop on open hardware. `invasive` `PAPER+CODE`
- [ROS-Neuro](https://github.com/rosneuro) — Open-source neurorobotic middleware placing neural interfaces and ROS-based robotics at the same conceptual level; ships LSL and `eegdev` acquisition plugins plus real-time filters. `real-time-capable` `non-invasive` `FRAMEWORK` · C++ (ROS 1/Noetic) · Frontiers in Neurorobotics, 2022

## 6. Full-Stack Platforms

- [Dareplane](https://github.com/bsdlab/Dareplane) — Modular, technology-agnostic Python platform purpose-built for closed-loop BCI research, validated in a live adaptive-DBS session and a c-VEP speller (J. Neural Eng., 2025). `real-time-capable` `invasive` `non-invasive` `FRAMEWORK` · Python
- [BCI2000](https://www.bci2000.org/) — Long-standing C++ platform for acquisition, stimulus presentation, and closed-loop BCI; widely used in clinical/academic aDBS and BCI research. `real-time-capable` `FRAMEWORK` · C++
- [OpenViBE](https://openvibe.inria.fr/) — Visual-programming platform for designing and running BCI pipelines without writing full applications. `real-time-capable` `FRAMEWORK` · C++, visual scripting
- [NeuroPype](https://www.neuropype.io/) — Real-time platform for BCI, neuroimaging, and signal processing across EEG/fNIRS/ExG modalities; free tier + commercial. `real-time-capable` `FRAMEWORK`
- [CLoSES-RT](https://github.com/Center-For-Neurotechnology/CLoSES-RT) — Simulink Real-Time platform for closed-loop intracranial stimulation in humans. `real-time-capable` `invasive` `FRAMEWORK` · MATLAB/Simulink

**Which one?** See [`docs/engineering-notes/what-to-use.md`](docs/engineering-notes/what-to-use.md) for evidence-based defaults by use case (not a ranking).

## 7. Datasets & Standards

- [BIDS Specification — EEG/iEEG](https://bids-specification.readthedocs.io/) — The organizing standard for electrophysiology datasets. `STANDARD`
- [OpenNeuro](https://openneuro.org/) — Largest open repository of BIDS-formatted EEG/iEEG/MEG datasets. `DATASET`
- [Neurodata Without Borders (NWB)](https://github.com/NeurodataWithoutBorders/nwb-schema) — The standard for spike-level and intracellular/extracellular electrophysiology data (where BIDS is thin); PyNWB and MatNWB APIs. `STANDARD` · BSD
- [openlists/ElectrophysiologyData](https://github.com/openlists/ElectrophysiologyData) — Curated index of openly available human electrophysiology datasets, including intracranial data. `REFERENCE LIST`
- [PhysioNet](https://physionet.org/) — Long-running repository of physiological signal datasets (EEG, EMG, ECG) with a strong reproducibility track record. `DATASET`

See [`docs/data/README.md`](docs/data/README.md) for which standard fits which modality, and why BIDS and NWB solve different problems rather than competing.

---

## Reference Architectures & System Recipes

The most differentiating part of this repository, now covering 6 recipes at varying levels of maturity — clearly labeled as **conceptual**, **research prototype**, **experimentally demonstrated**, or **clinically validated**. Each names real tools, states data/calibration requirements, and gives the actual bottleneck rather than a generic "latency matters" caveat.

→ **[Full recipe index in `docs/systems/README.md`](docs/systems/README.md)**

| Recipe | Modality | Maturity |
|---|---|---|
| [EEG → cursor/robot control](docs/systems/eeg-motor-bci.md) | EEG | Experimentally demonstrated |
| [Adaptive DBS](docs/systems/adaptive-dbs.md) | LFP/ECoG | Experimentally demonstrated (benchtop + first-in-human) |
| [Speech neuroprosthesis](docs/systems/speech-neuroprosthesis.md) | Intracortical | Clinically validated (single-participant trials) |
| [EMG-based prosthetic control](docs/systems/emg-prosthetic-control.md) | EMG | Clinically validated (commercial + research) |
| [EEG neurofeedback](docs/systems/eeg-neurofeedback.md) | EEG | Experimentally demonstrated |
| [Neuro-robotics via ROS](docs/systems/neuro-robotics-ros.md) | EEG/ECoG | Research prototype |

---

## Engineering Notes

Deep-dive conceptual treatments that don't fit a resource-list format:

- [**Timing & Reliability**](docs/engineering-notes/timing-and-reliability.md) — latency budgets, jitter, clock drift, fail-safe design, what to actually measure
- [**Control & Adaptation**](docs/engineering-notes/control-and-adaptation.md) — Kalman/MPC/RL for BCI control, calibration reduction, drift, personalization
- [**Benchmarking & Validation**](docs/engineering-notes/benchmarking.md) — what makes a closed-loop benchmark trustworthy; paper-reported vs. reproduced vs. standardized
- [**Safety, Security & Reliability**](docs/engineering-notes/safety-and-security.md) — neurosecurity, fail-safe states, established resources only
- [**What to Use**](docs/engineering-notes/what-to-use.md) — evidence-based stack recommendations by use case
- [**Open Questions**](docs/engineering-notes/open-questions.md) — where the ecosystem is still technically immature

---

## Interoperability & Simulation

- [**How do I connect X to Y?**](docs/interoperability/README.md) — acquisition↔LSL, LSL↔MNE, decoder↔ROS2, decoder↔stimulator, and where each bridge actually lives today
- [**Simulation & Hardware-in-the-Loop**](docs/simulation/README.md) — testing a closed-loop system before it touches a subject

---

## Software Ecosystem

Language/framework relevance is tracked inline via tags on each resource above (Python, C++, MATLAB, etc.), not as a separate taxonomy — most of this field is Python-first for decoding/analysis and C++ for the real-time acquisition/control core, with MATLAB still common in clinical/DBS research (Simulink Real-Time, CLoSES-RT). ROS/ROS2 is the standard integration layer once actuation is robotic (§5, ROS-Neuro). We do not maintain a generic "awesome Python" or "awesome C++" section — that's out of scope; see [`docs/CRITERIA.md`](docs/CRITERIA.md).

---

## Contributing

Contributions are welcome — this stays useful by staying small and high-signal.

1. Read [`docs/CRITERIA.md`](docs/CRITERIA.md) — every entry must pass the inclusion checklist, including the resource-type label (`TOOL`, `FRAMEWORK`, `HARDWARE`, `DATASET`, `STANDARD`, `PAPER+CODE`, `PROTOCOL`, `SIMULATOR`, `BENCHMARK`, `REFERENCE LIST`).
2. One resource per PR, with a one-sentence description explaining *why it matters to closed-loop work specifically*.
3. See [`CONTRIBUTING.md`](CONTRIBUTING.md) for format and process.

## License

[CC0](LICENSE) applies to this repository's own content (README, structure, curation, docs). It does **not** apply to linked external projects — each keeps its own license, noted inline where known. See the [LICENSE](LICENSE) file for the full scope statement. Should this repository come to contain substantial original software code (currently it doesn't — only markdown and minimal CI config), that code would carry its own license (e.g. MIT), not CC0.
