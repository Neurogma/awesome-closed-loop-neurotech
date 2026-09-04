# Control & Adaptation

This is the layer that makes this repository useful to robotics/control engineers, not just neuroscientists — and it's deliberately written as a conceptual treatment with primary-source citations rather than a tool list, because most control logic in this field lives as bespoke code inside lab repositories (e.g., BrainGate's decoders), not as reusable packaged libraries. Where a real open-source tool exists, it's linked; where the honest answer is "read the paper, the code is study-specific," we say so.

## Control theory relevant to closed-loop neurotechnology

**Thresholding / finite-state machines.** The simplest and still most common control layer in this repository's recipes (see Recipes 2, 4, 5) — map a decoded feature or class against a threshold, transition between defined states. Favored in clinical contexts (Recipe 2) precisely because it's interpretable and auditable, which matters for safety review.

**Kalman filtering.** The historically dominant approach for continuous intracortical cursor control — a linear Kalman decoder maps neural activity to cursor velocity with an underlying state-space model of movement dynamics. This is well-established enough that it's the standard baseline against which newer decoders (LSTMs, GP-based methods) are compared in the literature — see Hosman et al. (arXiv:1812.09835) for a direct RNN-vs-Kalman comparison on real BrainGate trial data, and Hochberg-lab work on the GP-DKF approach for reducing calibration time while retaining Kalman-filter-like interpretability. We are not aware of a standalone, actively maintained open-source package implementing a BCI-specific Kalman decoder — this is typically implemented directly inside a lab's experimental codebase (e.g., as part of the `speechBCI`/`nejm-brain-to-text` training pipelines' broader ecosystem) rather than as a general-purpose library.

**Model predictive control (MPC) and Bayesian state estimation.** More common in the robotics/actuation side of a closed-loop system (e.g., trajectory planning for a robotic arm receiving BCI commands) than in the neural decoding layer itself. If your recipe involves a robotic actuator (Recipe 6), the relevant control-theory literature and tooling is standard robotics MPC/state-estimation work, not neurotech-specific — ROS's `ros2_control` ecosystem is the practical entry point.

**Reinforcement learning and shared autonomy.** An active research direction for BCI control, particularly where the system assists a noisy/low-bandwidth neural signal by inferring user intent probabilistically and having the robot/cursor "help" — sometimes called shared autonomy or assistive teleoperation. This is genuinely promising but should be understood as a research direction, not a deployed technique with mature open tooling in the neurotech context specifically.

## Adaptation & personalization

These are named, real, unsolved-to-varying-degrees engineering problems in this field:

- **Calibration burden.** Most decoders in this repository (motor imagery, intracortical cursor control, speech) require participant-specific calibration data before use. Reducing this burden is an active research area — the GP-DKF work cited above (3-minute calibration to plateau performance) is one concrete published result worth knowing about.
- **Co-adaptation.** In closed-loop BCI, the user adapts to the decoder *while* the decoder is (sometimes) being updated based on the user's behavior — a genuinely two-sided learning problem, not just a one-sided ML training problem. This is part of why offline decoder accuracy is a weak proxy for online closed-loop performance (see Recipe 1's validation section).
- **Cross-session / cross-day drift.** Neural signals recorded from the same electrodes change over hours to months, for both non-invasive (electrode placement, skin impedance) and invasive (tissue response, chronic recording instability) reasons. The LINK dataset (see [Datasets](../data/README.md)) exists specifically to give the field a standard benchmark for studying this.
- **Cross-subject / cross-session generalization.** A decoder trained on one person's (or one day's) data typically does not transfer well to another person or another day without recalibration — this remains one of the field's clearest open problems; see [Open Questions](open-questions.md).
- **Online/continual learning.** Updating a decoder incrementally during use, rather than only via a separate offline calibration phase, risks catastrophic drift if not carefully constrained — an acknowledged tension in the adaptive-decoder literature, not a solved problem.

## How this maps onto the recipes in this repository

| Recipe | Dominant control approach | Adaptation concern |
|---|---|---|
| EEG motor BCI | Threshold / simple classifier | Per-session calibration; co-adaptation |
| Adaptive DBS | Threshold on extracted biomarker | Patient-specific biomarker selection |
| Speech neuroprosthesis | RNN decoder + LM rescoring (not classical control theory) | Chronic signal drift (LINK dataset) |
| EMG prosthetic control | Threshold / proportional mapping | Electrode shift over a day of wear |
| Neurofeedback | Threshold / continuous mapping | Session-to-session baseline drift |
| Neuro-robotics (ROS) | Downstream ROS control (MPC/state machines) once a command is decoded | Inherits the underlying decoder's adaptation concerns |
