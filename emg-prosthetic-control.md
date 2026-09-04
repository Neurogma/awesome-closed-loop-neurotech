# Recipe 4: EMG-Based Prosthetic Control

**Maturity: Clinically validated.** Myoelectric prosthetic control is the most mature closed-loop neurotechnology in real-world clinical use today — commercial myoelectric hands have been deployed for decades. The *research frontier* covered here (real-time motor-unit-level decoding) is newer and sits at experimentally-demonstrated maturity; conventional envelope-based myoelectric control is long-established clinical practice.

## Use case
Controlling a prosthetic hand or arm using electrical signals from residual forearm/upper-arm muscles, for someone with an upper-limb amputation or congenital limb difference.

## Architecture

| Stage | Tool | Notes |
|---|---|---|
| Sensing | Surface EMG electrodes over residual muscle | Non-invasive; peripheral, not central, nervous system |
| Acquisition | [BrainFlow](https://github.com/brainflow-dev/brainflow) (several supported boards include EMG channels), or dedicated EMG acquisition hardware | |
| Preprocessing | Bandpass + notch filtering, envelope extraction (RMS/moving average) | Classical approach for commercial myoelectric control |
| Advanced decoding (research) | [I-Spin live](https://github.com/simonavrillon/I-Spin) — real-time blind-source-separation decomposition into individual motor unit firing activity | This is the research-grade alternative to envelope-based control, decoding at the motor-neuron level rather than the whole-muscle level |
| Control | Threshold/proportional mapping (classical), or motor-unit discharge rate mapping (research) | Classical commercial systems use 2–4 discrete control channels; research systems aim for more degrees of freedom |
| Actuation | Prosthetic hand/arm — servo-driven fingers, wrist rotation | Commercial (clinical) or research 3D-printed/open-hardware prosthesis |
| Feedback | Visual (watching the prosthesis move); some research systems add haptic feedback | Standard commercial prostheses have no sensory feedback loop back to the user — this is itself an open engineering problem |

## Data & calibration requirements
Classical envelope-based control needs a short calibration (minutes) to set thresholds per muscle site. Motor-unit-level decomposition (I-Spin live) requires a baseline contraction to identify separation vectors ("motor unit filters") per motor unit before real-time use — validated in the original paper against synthetic EMG and five lower-limb muscles.

## Latency bottleneck
Envelope-based control can run with very short windows (tens of ms) since it doesn't need to resolve individual spikes — this is part of why myoelectric control has been clinically viable for so long. Motor-unit decomposition trades some of that speed for finer-grained control resolution; the accuracy/latency trade-off is explicitly characterized in the I-Spin live validation.

## Reliability concerns
- Electrode shift (the prosthetic socket moving relative to the skin) degrades signal quality over a day of wear — a well-known clinical problem, distinct from anything software can fully solve.
- Sweat and skin impedance changes affect signal quality over time.
- Motor-unit decomposition accuracy depends on the muscle and contraction intensity relative to the baseline calibration — the original validation explicitly reports how accuracy varies with this mismatch.

## Validation strategy
For classical systems: functional task performance (grasp success rate, time-to-grasp) is the standard clinical outcome measure. For motor-unit-level research systems: rate of agreement between real-time and offline (manually edited) motor unit spike trains, as reported in the I-Spin live validation.

## What's realistically reproducible vs. what requires specialized/clinical infrastructure
**Reproducible:** the entire signal chain from surface EMG through motor-unit decomposition, using I-Spin live and accessible EMG hardware, without needing an amputee participant — the tool was validated on intact-limb muscle recordings.
**Requires more:** clinical-grade prosthetic hardware, socket fitting, and amputee-participant studies require a clinical/prosthetics engineering context that a software engineer alone cannot replicate.
