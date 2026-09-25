# Source: https://docs.rewire.run/urdf-tf/urdf

URDF & TF

# URDF Loading

Automatic robot model visualization from /robot\_description.

Rewire automatically detects topics ending in `/robot_description` and loads the URDF for 3D visualization in the Rerun viewer.

## [How it works](https://docs.rewire.run/urdf-tf/urdf#how-it-works)

When Rewire receives a message on a `/robot_description` topic (typically `std_msgs/String`):

1. Parses the URDF XML to extract the robot name
2. Loads the robot model geometry using Rerun's `UrdfDataLoader`
3. Logs the model as an `Asset3D` archetype
4. Logs the raw URDF text as a `TextDocument` for inspection

The robot model appears in the 3D viewport with all links and joints.

## [Mesh resolution](https://docs.rewire.run/urdf-tf/urdf#mesh-resolution)

URDF files often reference meshes with `package://` URLs. For these to resolve, Rewire needs the ROS 2 package paths:

```
export AMENT_PREFIX_PATH=/opt/ros/humble/share
rewire record
```

If `AMENT_PREFIX_PATH` is not set, Rewire will log a warning and skip mesh loading. The robot skeleton (links and joints) will still be visible.

Mesh resolution also applies to `visualization_msgs/Marker` messages that use the `MESH_RESOURCE` type. Supported mesh formats: GLB, glTF, OBJ, STL, and DAE/COLLADA. URI schemes `package://`, `file://`, and absolute paths are all supported.

## [Multi-robot support](https://docs.rewire.run/urdf-tf/urdf#multi-robot-support)

Rewire derives a TF frame prefix from the topic namespace of each `/robot_description` topic. This lets multiple robots coexist in the same viewer without frame name collisions:

| Topic | Frame prefix | Example frame |
| --- | --- | --- |
| `/robot_description` | _(none)_ | `base_link` |
| `/robot1/robot_description` | `/robot1/` | `/robot1/base_link` |
| `/ns1/ns2/robot_description` | `/ns1/ns2/` | `/ns1/ns2/base_link` |

The prefix uses a leading `/` to match the ROS TF convention. Each robot appears as a separate entity tree in the Rerun viewer, with its own set of TF frames.

## [Custom message types](https://docs.rewire.run/urdf-tf/urdf#custom-message-types)

If your robot description is published on a non-standard topic or message type, you need to source your ROS 2 workspace so Rewire can deserialize the message:

```
source /opt/ros/humble/setup.bash
source ~/my_ws/install/setup.bash
rewire record
```

Workspace sourcing is also needed for [custom type resolution](https://docs.rewire.run/concepts/discovery#type-resolution) on pre-Jazzy distributions.

[Supported Types\\ \\ The built-in ROS 2 to Rerun type mappings.](https://docs.rewire.run/supported-types) [Transform Tree\\ \\ Full TF tree visualization with coordinate frames and axes.](https://docs.rewire.run/urdf-tf/transform-tree)

### On this page

[How it works](https://docs.rewire.run/urdf-tf/urdf#how-it-works) [Mesh resolution](https://docs.rewire.run/urdf-tf/urdf#mesh-resolution) [Multi-robot support](https://docs.rewire.run/urdf-tf/urdf#multi-robot-support) [Custom message types](https://docs.rewire.run/urdf-tf/urdf#custom-message-types)

Last updated on August 26, 2026

© 2026 Rewire