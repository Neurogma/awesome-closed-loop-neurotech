# Recipe 5: EEG Neurofeedback

**Maturity: Experimentally demonstrated.** Neurofeedback protocols are widely studied and published, but efficacy and mechanism for specific clinical applications (ADHD, anxiety, etc.) remain scientifically debated — this recipe describes the engineering architecture, which is well-established, separately from any clinical efficacy claim, which is not something this repository takes a position on.

## Use case
Training a user to modulate their own brain activity (e.g., a specific frequency band) by giving them real-time feedback about that activity — used in research on self-regulation, attention training, and as an experimental intervention in various conditions.

## Architecture

| Stage | Tool | Notes |
|---|---|---|
| Sensing | Any BrainFlow-supported EEG board | Non-invasive |
| Acquisition | [BrainFlow](https://github.com/brainflow-dev/brainflow) | |
| Synchronization | [LSL](https://github.com/sccn/labstreaminglayer) | |
| Preprocessing | [MNE-LSL](https://github.com/mne-tools/mne-lsl), bandpass filtering to the target frequency band | |
| Feature extraction | Band power in the target frequency (e.g., alpha 8–12 Hz, SMR 12–15 Hz), often as a ratio (e.g., theta/beta) | The specific feature is protocol-dependent — this is the actual "content" of a neurofeedback protocol design |
| Decision | Threshold or continuous mapping of feature → feedback signal | Often a z-score or percentile relative to a baseline recorded at session start |
| Feedback | Visual (bar, game element), auditory (tone pitch/volume), or combined | This *is* the actuation/output for this recipe — there's no external stimulator or robot |
| Loop closure | The user's own brain, adapting based on the feedback they receive | The "actuator" here is the human learning process itself, which is qualitatively different from the other recipes in this repository |

## Data & calibration requirements
A baseline recording (eyes-open/eyes-closed rest, typically a few minutes) is standard practice to establish the individual's reference band power before setting feedback thresholds. Unlike motor-imagery BCI, there's typically no separate "training the classifier" step — the "training" here is the human learning to self-regulate, which happens across the feedback session(s) themselves.

## Latency bottleneck
Feedback latency tolerance is generally looser than motor-control or stimulation recipes — hundreds of milliseconds of lag in a visual feedback bar is common and doesn't necessarily break the training paradigm, unlike a cursor-control BCI where added latency directly degrades usability. That said, feature-extraction windows still trade estimation stability against responsiveness the same way they do elsewhere in this repository.

## Reliability concerns
- Artifact contamination (eye blinks, muscle tension) can masquerade as the target signal if not filtered — a participant "learning" to blink strategically to move a feedback bar is a well-known confound in this literature, not a hypothetical.
- Session-to-session baseline drift means thresholds calibrated on day 1 may not be appropriate by day 10.

## Validation strategy
Within-session: does the target feature actually move in the trained direction over the course of a session, compared to a sham/control condition? Across studies: neurofeedback efficacy research is an active and sometimes contested area — this repository does not adjudicate clinical efficacy claims; see [`docs/CRITERIA.md`](../CRITERIA.md) for why we stay in the engineering lane.

## What's realistically reproducible vs. what requires specialized/clinical infrastructure
**Reproducible:** this entire recipe, end-to-end, is one of the most accessible in this repository — consumer EEG hardware, open software, no clinical population required to test the engineering pipeline itself.
**Requires more:** any claim about therapeutic efficacy for a specific condition requires a properly controlled clinical study — the engineering pipeline being reproducible does not mean a specific clinical protocol is validated.
