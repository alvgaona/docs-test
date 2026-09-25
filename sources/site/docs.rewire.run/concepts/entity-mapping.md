# Source: https://docs.rewire.run/concepts/entity-mapping

# Entity Mapping

How ROS 2 topics become Rerun entities in the viewer.

When Rewire receives a ROS 2 message, it converts it to one or more Rerun entities. Understanding this mapping helps you navigate the viewer and write custom mappings.

## [Topic to entity path](https://docs.rewire.run/concepts/entity-mapping#topic-to-entity-path)

ROS 2 topic names map directly to Rerun entity paths:

| ROS 2 topic | Rerun entity path |
| --- | --- |
| `/camera/image` | `/camera/image` |
| `/odom` | `/odom` |
| `/robot/joint_states` | `/robot/joint_states` |

The topic name becomes the entity path. Namespaced topics naturally create a hierarchy in the Rerun viewer's entity tree.

## [One topic, multiple entities](https://docs.rewire.run/concepts/entity-mapping#one-topic-multiple-entities)

Some message types produce more than one Rerun entity. For example:

- **`Odometry`** logs both an `InstancePoses3D` (pose) and `Scalars` (position, orientation, and linear/angular velocity)
- **`Imu`** logs `Arrows3D` (acceleration/angular velocity vectors) and `Scalars` (orientation, angular velocity, linear acceleration, and their covariance diagonals)
- **`PoseWithCovarianceStamped`** logs `InstancePoses3D` (pose) and `Ellipsoids3D` (covariance)

The additional entities are logged as children of the topic path. For example, a `/odom` topic carrying `Odometry` messages produces:

```
/odom                 -> InstancePoses3D (pose)
/odom/position        -> Scalars (x, y, z)
/odom/orientation     -> Scalars (x, y, z, w)
/odom/twist/linear    -> Scalars (x, y, z)
/odom/twist/angular   -> Scalars (x, y, z)
```

## [Grouped scalar series](https://docs.rewire.run/concepts/entity-mapping#grouped-scalar-series)

A vector-valued signal logs as **one** entity carrying its components as named series, not one entity per axis. The three components of `/odom/position` share a single plot, colored by axis, and the legend names them `x`, `y`, and `z`.

To look at one axis on its own, click a series in the plot legend to toggle it, or Alt-click to solo it. The choice is stored in the blueprint, so it survives across sessions.

Before v0.7.0 each axis had its own entity, such as `/odom/position/x`. Blueprints that reference per-axis paths need updating to the grouped path.

The `BatteryState` converter is the exception: its fields mix units (V, A, Ah, %, °C) that would collide on one y-axis, so each keeps its own entity. Custom JSON5 mappings also keep the paths you define.

Messages of type `visualization_msgs/Marker` use a namespace and ID scheme. A marker published on the `/visualization_marker` topic with namespace `obstacles` and ID `3` produces:

```
/visualization_marker/obstacles/3  -> Boxes3D, Arrows3D, etc.
```

## [TF tree integration](https://docs.rewire.run/concepts/entity-mapping#tf-tree-integration)

Entities that have a `Header` with a `frame_id` are automatically placed in the TF tree. The Rerun viewer uses this to render data in the correct coordinate frame.

For example, a `PointCloud2` on `/lidar/points` with `frame_id: "lidar_link"` will appear attached to the `lidar_link` frame in 3D space, moving with the robot as TF updates.

## [Custom mappings](https://docs.rewire.run/concepts/entity-mapping#custom-mappings)

When the built-in type mappings don't cover your message type, you can define custom mappings in a JSON5 file. Custom mappings follow the same entity path convention — the topic name becomes the entity path, and you define which fields map to which Rerun archetype components.

See [Custom Mappings](https://docs.rewire.run/custom-mappings/overview) for details.

[Architecture\\ \\ How Rewire connects to your ROS 2 network without being a ROS 2 node.](https://docs.rewire.run/concepts/architecture) [Topic Discovery\\ \\ How Rewire finds and subscribes to ROS 2 topics.](https://docs.rewire.run/concepts/discovery)

### On this page

[Topic to entity path](https://docs.rewire.run/concepts/entity-mapping#topic-to-entity-path) [One topic, multiple entities](https://docs.rewire.run/concepts/entity-mapping#one-topic-multiple-entities) [Grouped scalar series](https://docs.rewire.run/concepts/entity-mapping#grouped-scalar-series) [TF tree integration](https://docs.rewire.run/concepts/entity-mapping#tf-tree-integration) [Custom mappings](https://docs.rewire.run/concepts/entity-mapping#custom-mappings)

Last updated on August 26, 2026

© 2026 Rewire