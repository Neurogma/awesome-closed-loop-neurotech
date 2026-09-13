# Recipe 6: Neuro-Robotics via ROS

**Maturity: Research prototype.** ROS-Neuro is installable, peer-reviewed middleware (Frontiers in Neurorobotics, 2022) with a published closed-loop motor-imagery BMI demonstration (Beraldo et al., SMC 2020). The *general recipe* is labeled research prototype because there are still relatively few complete, reproducible public examples that combine all stages below.

## Use case
Driving a robot (wheelchair, mobile robot, robotic arm) using decoded neural signals, where the robotics side is built on ROS/ROS2 rather than a bespoke actuator interface.

## Architecture

| Stage | Tool | Notes |
|---|---|---|
| Sensing | EEG (most common) or ECoG | Non-invasive is far more common in published ROS-integrated work to date |
| Acquisition | [ROS-Neuro `rosneuro_acquisition_lsl`](https://github.com/rosneuro/rosneuro_acquisition_lsl) or `rosneuro_acquisition_eegdev` | Purpose-built acquisition plugins that publish neural data as standard ROS topics |
| Synchronization | [LSL](https://github.com/sccn/labstreaminglayer) feeding into ROS-Neuro, or ROS's own timestamping once inside the ROS graph | ROS-Neuro's acquisition layer is explicitly designed to place neural data streams "at the same conceptual and implementation level" as robotic sensor streams |
| Preprocessing | [ROS-Neuro filters](https://github.com/rosneuro/rosneuro_filters) — DC, Butterworth, common average reference (CAR), Laplacian, and windowing filters, all as ROS-native plugins | One of ROS-Neuro's main contributions is exposing standard EEG preprocessing as composable ROS nodes |
| Decoding | Not part of ROS-Neuro itself — bring your own (e.g., EEGNet, CSP+LDA from Recipe 1) publishing to a ROS topic | This remains a gap; see [Open Questions](../engineering-notes/open-questions.md) |
| Control | ROS-native control nodes (state machines, `ros2_control`, or custom) | Standard robotics practice once the decoded command is a ROS message |
| Actuation | Any ROS-compatible robot — mobile base, robotic arm, wheelchair | The recipe can use the existing ROS robotics ecosystem at this stage |
| Feedback | Robot's own motion/visual feedback to the user, plus any explicit UI | |

## Data & calibration requirements
Same as the underlying decoding recipe you plug in (typically Recipe 1's motor-imagery calibration requirements) — ROS-Neuro doesn't change the neuroscience, it changes the plumbing.

## Latency bottleneck
Two latency sources stack here: the neural decoding window (as in Recipe 1) plus ROS message-passing overhead, which is typically small (single-digit ms) compared to decoding latency but is not zero — worth measuring explicitly if you're chaining several ROS nodes rather than assuming it's negligible. See [Timing & Reliability](../engineering-notes/timing-and-reliability.md).

## Reliability concerns
- ROS node crashes/restarts need the same fail-safe thinking as any robotics system — a robot that loses its command source mid-motion needs a defined safe state, independent of anything neuroscience-specific.
- ROS-Neuro's own package activity is modest (single-digit stars/forks on individual packages as of this writing). It is better treated as an academic reference implementation than as a large, widely adopted open-source project. Verify current package status before depending on it beyond prototyping.

## Validation strategy
Task-level robot performance (successful navigation, successful grasp) combined with the underlying decoder's own validation (see Recipe 1). ROS's own tooling (rosbag/ros2 bag for recording, RViz for visualization) is directly reusable here.

## What's realistically reproducible vs. what requires specialized/clinical infrastructure
**Reproducible:** the ROS-Neuro acquisition and filtering layer, connected to a simulated or real robot in a ROS/Gazebo environment using consumer EEG hardware. This is feasible for an engineer familiar with both EEG basics and ROS.
**Requires more:** complete, robust, published end-to-end demonstrations at the level of Recipes 1–4 are less common here, so this recipe is better viewed as an emerging area.
