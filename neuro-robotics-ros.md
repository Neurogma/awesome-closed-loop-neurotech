# Recipe 6: Neuro-Robotics via ROS

**Maturity: Research prototype.** ROS-Neuro itself is real, installable, peer-reviewed middleware (Frontiers in Neurorobotics, 2022) with a published closed-loop motor-imagery BMI demonstration (Beraldo et al., SMC 2020). We label the *general recipe* as research prototype because complete, reproducible public examples combining all stages below are still relatively few — this is an honest reflection of the ecosystem's current maturity, not a knock on the tools involved.

## Use case
Driving a robot (wheelchair, mobile robot, robotic arm) using decoded neural signals, where the robotics side is built on ROS/ROS2 rather than a bespoke actuator interface.

## Architecture

| Stage | Tool | Notes |
|---|---|---|
| Sensing | EEG (most common) or ECoG | Non-invasive is far more common in published ROS-integrated work to date |
| Acquisition | [ROS-Neuro `rosneuro_acquisition_lsl`](https://github.com/rosneuro/rosneuro_acquisition_lsl) or `rosneuro_acquisition_eegdev` | Purpose-built acquisition plugins that publish neural data as standard ROS topics |
| Synchronization | [LSL](https://github.com/sccn/labstreaminglayer) feeding into ROS-Neuro, or ROS's own timestamping once inside the ROS graph | ROS-Neuro's acquisition layer is explicitly designed to place neural data streams "at the same conceptual and implementation level" as robotic sensor streams |
| Preprocessing | [ROS-Neuro filters](https://github.com/rosneuro/rosneuro_filters) — DC, Butterworth, common average reference (CAR), Laplacian, and windowing filters, all as ROS-native plugins | This is one of ROS-Neuro's clearest contributions: standard EEG preprocessing exposed as composable ROS nodes |
| Decoding | Not part of ROS-Neuro itself — bring your own (e.g., EEGNet, CSP+LDA from Recipe 1) publishing to a ROS topic | This is a genuine current gap — see [Open Questions](../engineering-notes/open-questions.md) |
| Control | ROS-native control nodes (state machines, `ros2_control`, or custom) | Standard robotics practice once the decoded command is a ROS message |
| Actuation | Any ROS-compatible robot — mobile base, robotic arm, wheelchair | This is where the recipe inherits the entire ROS robotics ecosystem essentially for free |
| Feedback | Robot's own motion/visual feedback to the user, plus any explicit UI | |

## Data & calibration requirements
Same as the underlying decoding recipe you plug in (typically Recipe 1's motor-imagery calibration requirements) — ROS-Neuro doesn't change the neuroscience, it changes the plumbing.

## Latency bottleneck
Two latency sources stack here: the neural decoding window (as in Recipe 1) plus ROS message-passing overhead, which is typically small (single-digit ms) compared to decoding latency but is not zero — worth measuring explicitly if you're chaining several ROS nodes rather than assuming it's negligible. See [Timing & Reliability](../engineering-notes/timing-and-reliability.md).

## Reliability concerns
- ROS node crashes/restarts need the same fail-safe thinking as any robotics system — a robot that loses its command source mid-motion needs a defined safe state, independent of anything neuroscience-specific.
- ROS-Neuro's own package activity is real but modest (single-digit stars/forks on individual packages as of this writing) — treat it as a solid academic reference implementation, not a large, battle-tested open-source community project. Verify current package status before depending on it for anything beyond prototyping.

## Validation strategy
Task-level robot performance (successful navigation, successful grasp) combined with the underlying decoder's own validation (see Recipe 1). ROS's own tooling (rosbag/ros2 bag for recording, RViz for visualization) is directly reusable here.

## What's realistically reproducible vs. what requires specialized/clinical infrastructure
**Reproducible:** the ROS-Neuro acquisition and filtering layer, connected to a simulated or real robot in a ROS/Gazebo environment, using consumer EEG hardware — this is a genuinely buildable weekend-to-month project for an engineer comfortable with both EEG basics and ROS.
**Requires more:** a complete, robust, published end-to-end closed-loop demonstration at the level of Recipes 1–4 is less established here — treat this recipe as the frontier, not the well-trodden path.
