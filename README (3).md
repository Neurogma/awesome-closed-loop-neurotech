# Closed-Loop System Recipes

Six reference architectures, each a realistic engineering sketch of how the building blocks in the main README compose into a working closed loop. These are **educational**, not validated protocols you can deploy as-is — but each names real tools, real papers, and real bottlenecks rather than generic placeholders.

## Maturity labels — read this first

Every recipe is tagged with one of four maturity levels. These are not quality ratings — a conceptual architecture can be perfectly sound engineering that simply hasn't been built end-to-end yet, and a clinically validated system can still have rough edges. The label tells you what kind of evidence backs the recipe:

| Label | Meaning |
|---|---|
| **Conceptual architecture** | A sound composition of existing tools that, to our knowledge, hasn't been demonstrated end-to-end as described. Treat as a starting design, not a proven path. |
| **Research prototype** | Built and run by a research group, typically reported in a paper or preprint, usually with non-human or small-N human data. |
| **Experimentally demonstrated** | Shown working end-to-end with human participants in a research/lab setting, with published results (benchtop or in-session). |
| **Clinically validated** | Demonstrated in a clinical trial context, typically with regulatory oversight (IRB/ethics approval, often FDA/CE pathway for components), and published in a clinical or high-tier venue. |

We do not use "clinically validated" to mean "approved medical device" — none of the recipes here describe an approved consumer/clinical product. It means the underlying claim was tested under clinical-trial conditions, which is a meaningfully higher evidence bar than a lab demo.

## Recipe index

| # | Recipe | Modality | Maturity | Realistically reproducible by you? |
|---|---|---|---|---|
| 1 | [EEG → Cursor / Robot Control](eeg-motor-bci.md) | EEG (non-invasive) | Experimentally demonstrated | Yes — consumer/research EEG hardware, open software |
| 2 | [Adaptive DBS](adaptive-dbs.md) | LFP/ECoG (invasive) | Experimentally demonstrated | No — requires clinical access to implanted/externalized hardware |
| 3 | [Speech Neuroprosthesis](speech-neuroprosthesis.md) | Intracortical (invasive) | Clinically validated | No — requires an implanted research participant; code/data are reproducible offline |
| 4 | [EMG-Based Prosthetic Control](emg-prosthetic-control.md) | EMG (peripheral, non-invasive) | Clinically validated | Yes — surface EMG hardware is accessible; clinical-grade prosthesis integration is not |
| 5 | [EEG Neurofeedback](eeg-neurofeedback.md) | EEG (non-invasive) | Experimentally demonstrated | Yes — the most accessible recipe here end-to-end |
| 6 | [Neuro-Robotics via ROS](neuro-robotics-ros.md) | EEG/ECoG | Research prototype | Partially — ROS-Neuro is real and installable, but few complete public examples exist |

## Every recipe answers the same 15 questions

To keep recipes comparable and to force honesty about what's actually known, each one is structured around:

1. Use case
2. Neural modality
3. Sensing hardware
4. Acquisition layer
5. Streaming layer
6. Synchronization
7. Preprocessing
8. Feature extraction
9. Decoding
10. Control
11. Actuation/stimulation
12. Feedback
13. Major open-source projects involved
14. Data & calibration requirements
15. Latency bottleneck, reliability concerns, and validation strategy

Plus an explicit **"what's realistically reproducible vs. what requires specialized/clinical infrastructure"** call-out, because conflating the two is one of the most common ways a resource list misleads engineers about feasibility.

## Contributing a recipe

New recipes are welcome via PR if they: name specific tools from the main README (not placeholders), answer all 15 questions above, state the bottleneck honestly, and carry an accurate maturity label with a citation for it. See [`CONTRIBUTING.md`](../../CONTRIBUTING.md).
