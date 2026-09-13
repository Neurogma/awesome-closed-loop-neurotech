# Closed-Loop System Recipes

Six reference architectures showing how the building blocks in the main README can be combined into a working closed loop. These are **educational**, not validated protocols for deployment. Each recipe names specific tools, papers, and bottlenecks instead of using generic placeholders.

## Maturity labels — read this first

Every recipe is tagged with one of four maturity levels. These are evidence levels, not quality ratings: a conceptual architecture can be sound engineering that has not yet been built end-to-end, while a clinically validated system can still have limitations. The label indicates the type of evidence behind the recipe:

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

To keep recipes comparable and make the evidence and limitations explicit, each one is structured around:

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

Each recipe also includes **"what's realistically reproducible vs. what requires specialized/clinical infrastructure"** so readers can distinguish a practical implementation from one that depends on specialized infrastructure.

## Contributing a recipe

New recipes are welcome via PR if they name specific tools from the main README, answer all 15 questions above, state the main bottleneck, and use an accurate maturity label with a supporting citation. See [`CONTRIBUTING.md`](../../CONTRIBUTING.md).
