# Recipe 3: Speech Neuroprosthesis

**Maturity: Clinically validated.** This recipe describes systems demonstrated in published clinical trials with implanted participants (Willett et al., Nature 2023; Card et al., NEJM 2024) — the strongest evidence tier in this repository. It is *not* an approved medical device, and the "clinically validated" label refers to trial-level demonstration, not regulatory clearance.

## Use case
Restoring communication for someone who cannot speak due to paralysis (e.g., ALS, brainstem stroke) by decoding attempted speech directly from neural activity.

## Architecture

| Stage | Tool | Notes |
|---|---|---|
| Sensing | Intracortical microelectrode arrays (e.g., Utah array in ventral precentral gyrus) | Clinical/research implant — not accessible outside a trial |
| Acquisition | Array-specific recording hardware | Typically 100–256+ channels, high sampling rate |
| Preprocessing | Real-time spike binning / threshold crossing | Converts raw voltage into firing-rate-like features, typically in ~10–20 ms bins |
| Decoding | RNN decoder — [speechBCI](https://github.com/fwillett/speechBCI) and [nejm-brain-to-text](https://github.com/Neuroprosthetics-Lab/nejm-brain-to-text) | Neural activity → phoneme probabilities |
| Language modeling | n-gram or neural LM rescoring (included in `nejm-brain-to-text`) | Phonemes → words; this stage is responsible for a large share of the accuracy gains, not just the neural decoder |
| Output | Text-to-speech, or direct voice synthesis in newer work | Some more recent systems synthesize voice directly rather than routing through text |
| Feedback | The participant hears/reads their own decoded output in real time | Critical for the participant to correct and adapt; this is a genuine human-in-the-loop adaptation problem, not just a UI nicety |

## Data & calibration requirements
These systems require substantial participant-specific training data collected over multiple sessions with the implanted array, plus periodic recalibration as neural signals drift over time (a well-documented phenomenon with chronic intracortical recordings — see the LINK dataset referenced in [Datasets](../data/README.md)). This is not a "download weights and go" system; the decoder is trained on that specific participant's neural activity.

## Latency bottleneck
The published gains from roughly 9% to sub-5% word-error-rate in this line of work come substantially from the language-model rescoring stage, not solely from improving the neural decoder. This is worth knowing before assuming "a better decoder" is always where to invest engineering effort — sometimes the language model is the higher-leverage improvement.

## Reliability concerns
- Chronic electrode signal degradation/drift over months to years is a known, active engineering problem (see the LINK dataset's explicit purpose of studying this).
- Decoder confidence and fallback behavior when neural signal quality drops matters for a communication-critical system in a way it may not for a research demo.

## Validation strategy
Published word-error-rate on held-out sessions with the same participant, and — increasingly — cross-session evaluation to test decoder stability over time (again, see the LINK dataset, designed specifically for this). Offline reproduction of the RNN decoder against publicly released data (via `speechBCI` and `nejm-brain-to-text`) is possible and is exactly what those repositories are designed to let you do.

## What's realistically reproducible vs. what requires specialized/clinical infrastructure
**Reproducible:** the decoder training and evaluation code, against the publicly released datasets from these trials (both repositories link their data). You can reproduce the offline decoding results reported in the papers.
**Requires clinical infrastructure:** the sensing side entirely — this recipe cannot be replicated end-to-end outside a clinical trial with an implanted participant. Treat the code repositories as a way to study and improve the decoding algorithms, not as a path to building your own end-to-end system.
