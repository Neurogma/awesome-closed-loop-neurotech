# Data & Data Infrastructure

Datasets and format standards, organized by the question that actually matters: **which standard fits which modality, and why they don't compete.**

## Format standards: BIDS vs. NWB vs. the file-level formats

**BIDS (Brain Imaging Data Structure) — EEG/iEEG.** BIDS defines directory structure, file naming, and metadata conventions for organizing a dataset — it's a convention layer, not a file format itself. It's the dominant standard for scalp EEG and iEEG (ECoG/depth electrode) datasets shared on [OpenNeuro](https://openneuro.org/). See the [BIDS Specification](https://bids-specification.readthedocs.io/).

**NWB (Neurodata Without Borders)** — the standard for spike-level, intracellular, and extracellular electrophysiology data, where BIDS's conventions are thin. NWB is a full file format (built on HDF5) with a schema for storing raw and processed data alongside rich metadata, plus PyNWB and MatNWB APIs for both languages. This is the right standard for intracortical/single-unit recordings (Recipe 3's domain) — see [`nwb-schema`](https://github.com/NeurodataWithoutBorders/nwb-schema).

**Why both exist, not competing:** BIDS grew out of the neuroimaging (fMRI) community and extended to EEG/iEEG; NWB grew out of the systems neuroscience/electrophysiology community with single-unit and intracellular data as first-class citizens from the start. If your recipe involves scalp or subdural electrodes at the population/field-potential level, reach for BIDS. If it involves spike-sorted single-unit data, reach for NWB. Several datasets increasingly bridge both.

**File-level formats you'll encounter underneath these standards:**
- **EDF (European Data Format)** — a long-standing, simple binary format for storing multi-channel physiological time series; still common for raw EEG/PSG data and often the format your acquisition hardware exports natively.
- **FIF** — MNE-Python/Neuromag's native format for M/EEG data, with rich metadata support; what you'll typically work with directly inside MNE-based pipelines (Recipe 1).
- **HDF5** — the general-purpose hierarchical data format NWB is built on; also common as a raw storage layer for large neural datasets outside any specific neuro-standard.
- **Zarr** — a newer, cloud/parallel-friendly array storage format gaining adoption for very large neural datasets (e.g., high-channel-count or long-duration recordings) where HDF5's single-writer model becomes a bottleneck; worth knowing about if you're working with unusually large datasets, though HDF5/NWB remains the more established default for now.

## Datasets

- [OpenNeuro](https://openneuro.org/) — largest open repository of BIDS-formatted EEG/iEEG/MEG datasets. `DATASET`
- [PhysioNet](https://physionet.org/) — long-running physiological signal dataset repository (EEG, EMG, ECG) with a strong reproducibility track record. `DATASET`
- [openlists/ElectrophysiologyData](https://github.com/openlists/ElectrophysiologyData) — curated meta-list of openly available human electrophysiology datasets, including intracranial data; we link here rather than duplicating individual dataset entries. `REFERENCE LIST`
- [LINK Dataset](https://openreview.net/forum?id=TAdeh1dLzu) — 312 sessions of non-human primate intracortical spiking + kinematics spanning 1,242 days (~3.5 years); purpose-built for studying long-term decoder stability, directly relevant to the cross-session drift problem discussed in [Open Questions](../engineering-notes/open-questions.md). `DATASET`
- [MOABB (Mother of All BCI Benchmarks)](https://github.com/NeuroTechX/moabb) — NeuroTechX-backed framework aggregating dozens of public motor-imagery/P300/SSVEP EEG datasets behind one API, with built-in within-session, cross-session, and cross-subject evaluation modes; J. Neural Eng. 2018, actively maintained. `TOOL` + `DATASET aggregator` · Python · BSD

## Why this matters to closed-loop engineering specifically

A closed-loop system's decoder is only as good as its training data's representativeness — and the cross-session/cross-subject datasets above (LINK, the multi-day motor-imagery datasets aggregated by MOABB) exist specifically because that representativeness gap is a known, active engineering problem, not a solved one. If you're evaluating a decoding approach, testing it against a multi-session dataset rather than a single-session dataset gives a meaningfully more honest picture of how it will perform in real closed-loop use.

## Provenance and reproducibility

We do not currently list a dedicated data/model-versioning tool specific to this field (e.g., a neurotech-specific DVC-style workflow) because we did not find one meeting this repository's bar for maturity and adoption — general-purpose data versioning tools (DVC, Git LFS) are usable here but aren't neurotech-specific, so we leave that choice to standard software-engineering practice rather than listing generic tools out of scope for this repository (see [`CRITERIA.md`](../CRITERIA.md)).
