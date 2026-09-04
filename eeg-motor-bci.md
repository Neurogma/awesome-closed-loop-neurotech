# Recipe 1: EEG → Cursor / Robot Control

**Maturity: Experimentally demonstrated.** Motor-imagery and SSVEP EEG control of cursors and simple robots is a mature research area with decades of published, reproducible results (see the BCI Competition datasets and EEGNet's original validation). This is the most accessible recipe in this repository to build yourself.

## Use case
Non-invasive motor-imagery or SSVEP-based BCI driving a cursor, wheelchair interface, or simple robot.

## Architecture

| Stage | Tool | Notes |
|---|---|---|
| Sensing | OpenBCI Cyton/Ganglion, or any [BrainFlow](https://github.com/brainflow-dev/brainflow)-supported board | Non-invasive, dry or wet electrodes, 8–16 channels typical |
| Acquisition | [BrainFlow](https://github.com/brainflow-dev/brainflow) | Board-agnostic; handles device-specific quirks |
| Synchronization | [LSL](https://github.com/sccn/labstreaminglayer) via BrainFlow→LSL bridge | Timestamps samples for alignment with stimulus/event markers |
| Preprocessing | [MNE-LSL](https://github.com/mne-tools/mne-lsl) + [ASR](https://github.com/sccn/clean_rawdata) | Bandpass filter (typically 8–30 Hz for motor imagery), online artifact rejection |
| Feature extraction | Common Spatial Patterns (CSP) or band power | CSP is the classical, data-efficient baseline; still competitive with small training sets |
| Decoding | [EEGNet](https://github.com/vlawhern/arl-eegmodels) or CSP+LDA | EEGNet if you have enough training data (typically hundreds of trials); CSP+LDA is the low-data fallback |
| Control | Threshold or small state machine | Maps decoder output → discrete command (left/right/select) |
| Actuation | Cursor, robot arm, wheelchair interface | Application-specific; [ROS-Neuro](https://github.com/rosneuro) if robotic |
| Feedback | Visual/auditory cue to the user | Closes the loop for human-side adaptation — see [co-adaptation](../engineering-notes/control-and-adaptation.md) |

## Data & calibration requirements
Motor-imagery decoders typically need a per-subject calibration session (tens of minutes, hundreds of trials) before online use — cross-subject transfer remains an open problem (see [Open Questions](../engineering-notes/open-questions.md)). SSVEP-based control needs less calibration but requires the user to fixate on flickering targets, which is a different UX trade-off.

## Latency bottleneck
Dominated by the classification window length, not compute. EEG decoders typically need 250 ms–1 s of data to decode reliably. Shortening the window trades accuracy for speed — this is the central design decision in this recipe, not a hyperparameter afterthought. SSVEP paradigms can achieve shorter effective decision times because the stimulus itself provides a strong, known-frequency signal.

## Reliability concerns
- Electrode impedance drift over a session degrades signal quality — check impedance, not just that the stream is alive.
- Motor-imagery decoders can silently degrade as the user fatigues; there is no built-in confidence signal unless you add one (e.g., a rejection threshold on decoder output probability).
- SSVEP performance depends on ambient lighting and screen refresh rate matching your stimulus frequencies.

## Validation strategy
Offline cross-validation accuracy is a weak proxy for online closed-loop performance — a decoder that looks good offline can perform worse online because the user adapts to *it* in real time (co-adaptation effects). If you can, validate with actual closed-loop sessions, not just held-out offline data. See [`docs/simulation/README.md`](../simulation/README.md) for testing with [`jtcostello/bcisimulator`](https://github.com/jtcostello/bcisimulator) before running real subjects.

## What's realistically reproducible vs. what requires specialized infrastructure
**Reproducible:** the entire pipeline above, with consumer-grade OpenBCI hardware, in a home or lab setting, by a single engineer.
**Requires more:** robust performance across sessions/days without recalibration, and any claims of "able-bodied-approaching" performance — those come from tightly controlled lab studies, not off-the-shelf hardware.
