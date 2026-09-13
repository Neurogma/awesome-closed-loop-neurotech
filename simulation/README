# Simulation & Hardware-in-the-Loop

**The question this page answers: "How can an engineer test a closed-loop neurotechnology system before connecting it to a human?"**

This is one of the areas where a curated map is useful because the available tooling is real but limited. Knowing what exists, and where the gaps are, avoids spending time looking for tools that are not there.

## Closed-loop decoder simulation

- [`jtcostello/bcisimulator`](https://github.com/jtcostello/bcisimulator) — A simple, purpose-built closed-loop BCI simulator for testing real-time neural decoding algorithms against synthetic neural data with a configurable tuning model, noise level, and channel count. Explicitly documented as approximating rather than physiologically modeling real neural data — the maintainers are upfront that simulated signals lack features like trial-timing variance present in real data. `SIMULATOR` · Python

**Why this matters specifically for closed-loop work:** it lets you iterate on decoder architecture and control-loop logic (Recipe 1, and by extension any recipe with a decode→control→feedback loop) without using live subject time for every test. The simulator runs the loop rather than providing only a static synthetic dataset, so it also exercises timing and control logic that offline data cannot.

**Its main limitation:** it uses a simple log-linear tuning model for simulated neural data. A new simulator instance is generated per decoder, so comparing two decoders side-by-side requires care (see the project's own documentation on this point). Treat results from this tool as useful for iterating on your own pipeline's engineering correctness, not as a substitute for real closed-loop validation before deployment.

## Synthetic signal generation for pipeline testing

Beyond decoder-level simulation, you often need to validate your acquisition→streaming→processing pipeline itself (not the decoder) before live hardware is available. The most robust pattern found in this research is: **publish synthetic or pre-recorded data over the same LSL stream format your real hardware would use**, so the rest of the pipeline is agnostic to whether the source is real or simulated. This is a pattern, not a single packaged tool — LSL's own tooling (e.g., example synthetic-stream senders included with liblsl) is the practical starting point, combined with replaying a previously recorded XDF file.

We did not find a widely-adopted, actively-maintained, general-purpose synthetic EEG/neural signal generator meeting this repository's bar for inclusion as a standalone tool — several research-group-specific generators exist (forward-modeling approaches, ARX-model-based generators) but are narrowly scoped to their originating study rather than general-purpose. If you're aware of one that should be added here, see [`CONTRIBUTING.md`](../../CONTRIBUTING.md).

## Hardware-in-the-loop (HIL) testing

For the adaptive-DBS recipe specifically, [Dareplane](https://github.com/bsdlab/Dareplane)'s own published validation methodology is the clearest public example of a HIL testing progression in this space: benchtop testing with an Arduino-based rig, before an implantable pulse generator, before a CE-certified external stimulator — escalating realism in controlled stages rather than jumping straight to the most realistic (and highest-risk) hardware. This progression is worth treating as a template even outside the DBS context: validate your control logic against a cheap, safe stand-in for your real actuator before connecting the real one.

## Robotics simulation

If your recipe involves a robotic actuator (Recipe 6), the ROS/ROS2 ecosystem already provides mature simulation tooling. Gazebo is the standard ROS-integrated physics simulator and is the practical default for testing a decoder-to-robot pipeline without physical hardware. We do not maintain a dedicated entry for Gazebo or other general-purpose robotics simulators because they are not neurotech-specific; see [`CRITERIA.md`](../CRITERIA.md). **For a ROS-based actuator, there is no need to build a separate neurotech-specific simulator.**

## Reinforcement learning environments

We did not find a maintained, adopted RL environment specifically designed for closed-loop neurotechnology (e.g., a standard Gym-style environment simulating a BCI decode→control loop for training shared-autonomy or adaptive-control policies). This remains a gap; see [Open Questions](../engineering-notes/open-questions.md).

## What "testing before touching a human" actually means in practice, by recipe

| Recipe | Practical simulation/HIL path today |
|---|---|
| EEG motor BCI | `bcisimulator` for decoder logic; replayed LSL/XDF data for pipeline testing |
| Adaptive DBS | Dareplane's benchtop (Arduino) progression before implantable/external hardware |
| Speech neuroprosthesis | Offline replay against publicly released datasets (`speechBCI`, `nejm-brain-to-text`) — no closed-loop simulator identified for this modality |
| EMG prosthetic control | I-Spin live was itself validated first against synthetic EMG before real muscle recordings — the same validate-in-simulation-first pattern |
| Neurofeedback | Replayed EEG data for pipeline testing; no dedicated closed-loop neurofeedback simulator identified |
| Neuro-robotics (ROS) | Gazebo for the robotics side; `bcisimulator` or replayed data for the decoding side |

This table includes the remaining gaps. Several cells describe the best available practice rather than a purpose-built tool because no such tool was identified for that combination.
