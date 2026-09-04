# Recipe 2: Adaptive DBS (Closed-Loop Neuromodulation)

**Maturity: Experimentally demonstrated.** Dareplane's benchtop validation across three hardware configurations plus a first-in-human closed-loop session with externalized leads (J. Neural Eng., 2025) is the strongest public reference point. This is not a clinically approved therapy pathway — it is validated research infrastructure.

## Use case
Adaptive/closed-loop deep brain stimulation research (e.g., Parkinson's disease, essential tremor), where stimulation parameters adjust in real time based on sensed neural biomarkers, instead of running continuous open-loop stimulation.

## Architecture

| Stage | Tool | Notes |
|---|---|---|
| Sensing | Externalized DBS/ECoG leads, or a sensing-enabled implantable pulse generator (IPG) | Requires clinical/research access — not a DIY layer |
| Acquisition | Device-specific API (e.g., research IPG SDK) | Hardware-dependent; often the least standardized layer in the whole stack |
| Synchronization | [LSL](https://github.com/sccn/labstreaminglayer), or platform-native (Dareplane's PCOMMS over TCP) | |
| Preprocessing + feature extraction | [py_neuromodulation](https://github.com/neuromodulation/py_neuromodulation) | Real-time LFP/ECoG band-power and related features, purpose-built for this |
| Decoding / decision | Threshold or lightweight classifier on extracted features | Simplicity is a feature here — interpretability matters clinically and for safety review |
| Orchestration | [Dareplane](https://github.com/bsdlab/Dareplane) | Coordinates acquisition, decision, and stimulation modules over TCP; the closest thing to a reference implementation of this whole recipe |
| Stimulation | Research neurostimulator — Dareplane's published validation used (a) an Arduino benchtop rig, (b) an implantable pulse generator, and (c) a CE-certified external neurostimulator, in that order of accessibility | |
| Feedback | The neural state itself — stimulation changes the signal being sensed | True physiological closed loop, not a UI feedback cue |

## Data & calibration requirements
Biomarker selection (which frequency band, which electrode contact) is typically patient-specific and determined in a separate sensing/mapping session before closed-loop operation begins. There is no off-the-shelf "biomarker" that works across patients — this is one of the field's genuine unsolved calibration burdens (see [Open Questions](../engineering-notes/open-questions.md)).

## Latency bottleneck
The safety-relevant latency budget is set by the clinical application, not by software convenience. Dareplane's published benchtop results are the closest public reference for what's achievable end-to-end on this stack — see the paper for the actual measured numbers across their three hardware configurations rather than relying on a general claim here. Regulatory and safety review, not software architecture, is usually the long pole for anything beyond benchtop testing.

## Reliability concerns
- Lead/electrode connection integrity over long sessions — dedicated hardware health monitoring is more critical here than in non-invasive recipes, because a silent sensing failure could mean stimulation is being driven by noise.
- Fail-safe design (falling back to a known-safe open-loop stimulation pattern if the closed-loop decision logic becomes unavailable) is a stated design goal of platforms like Dareplane, not an afterthought.
- See [Safety, Security & Reliability](../engineering-notes/safety-and-security.md) for what's established vs. still emerging in this space.

## Validation strategy
Benchtop validation with synthetic or replayed signals (via a simulated LSL stream) before any hardware-in-the-loop test, before any human session. Dareplane's own validation path — Arduino rig → implantable pulse generator → CE-certified external stimulator — is a reasonable template for escalating realism.

## What's realistically reproducible vs. what requires specialized/clinical infrastructure
**Reproducible:** the software stack (Dareplane + py_neuromodulation) against a benchtop rig or simulated data — this is genuinely something a research engineer can set up without clinical access.
**Requires clinical infrastructure:** everything involving actual implanted or externalized human leads, IRB/ethics approval, and clinical neurology/neurosurgery collaboration. Do not attempt to replicate the human-subject portion of this recipe outside a supervised clinical research setting.
