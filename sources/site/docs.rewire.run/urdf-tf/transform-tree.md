# Source: https://docs.rewire.run/urdf-tf/transform-tree

URDF & TF

# Transform Tree

Full TF tree visualization with coordinate frames and axes.

Rewire subscribes to `/tf` and `/tf_static` topics and builds the full transform tree in the Rerun viewer.

## [What gets visualized](https://docs.rewire.run/urdf-tf/transform-tree#what-gets-visualized)

- **`Transform3D`** — each transform in the tree as a 3D transformation
- **`CoordinateFrame`** — frame names with axes visualization

Both static and dynamic transforms are handled:

- `/tf_static` — logged once as static data (persists across the timeline)
- `/tf` — logged per frame, updating in real time

The `/tf_static` topic uses transient-local QoS — the publisher sends each frame once and expects late joiners to receive the cached sample. When several sources publish on `/tf_static`, raise the history Rewire keeps per latched topic with `--latched-depth <N>` (or `latched_history_depth` in the [config file](https://docs.rewire.run/configuration/json5-config#latched_history_depth)) so none of their frames are missed. Rewire also replays these frames after a [relay](https://docs.rewire.run/relay) reconnect and whenever a viewer joins the relay, so restarted and late-joining viewers still get a complete tree even after the relay's history cap evicted the original messages.

## [Coordinate frames](https://docs.rewire.run/urdf-tf/transform-tree#coordinate-frames)

Each frame in the TF tree appears as a named coordinate frame in Rerun's 3D viewport. Frames are hierarchical — child frames are positioned relative to their parent.

## [Combined with URDF](https://docs.rewire.run/urdf-tf/transform-tree#combined-with-urdf)

When both TF and URDF are available, Rewire:

1. Loads the robot model from `/robot_description`
2. Attaches it to the TF tree using frame IDs from the URDF joints
3. The robot model moves in real time as TF transforms update

This gives you a fully articulated robot visualization with no extra setup.

## [Multi-robot TF](https://docs.rewire.run/urdf-tf/transform-tree#multi-robot-tf)

When multiple robots publish on namespaced `/robot_description` topics (e.g., `/robot1/robot_description`, `/robot2/robot_description`), Rewire prefixes all TF frame names from each URDF with the topic namespace. This prevents frame name collisions — for example, both robots can define a `base_link` frame without conflict (`/robot1/base_link` and `/robot2/base_link`).

See [URDF Loading — Multi-robot support](https://docs.rewire.run/urdf-tf/urdf#multi-robot-support) for the prefix derivation rules.

[URDF Loading\\ \\ Automatic robot model visualization from /robot\_description.](https://docs.rewire.run/urdf-tf/urdf) [Diagnostics\\ \\ Per-topic Hz, bandwidth, drops, and latency monitoring.](https://docs.rewire.run/diagnostics)

### On this page

[What gets visualized](https://docs.rewire.run/urdf-tf/transform-tree#what-gets-visualized) [Coordinate frames](https://docs.rewire.run/urdf-tf/transform-tree#coordinate-frames) [Combined with URDF](https://docs.rewire.run/urdf-tf/transform-tree#combined-with-urdf) [Multi-robot TF](https://docs.rewire.run/urdf-tf/transform-tree#multi-robot-tf)

Last updated on August 26, 2026

© 2026 Rewire