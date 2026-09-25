# Source: https://docs.rewire.run/supported-types

# Supported Types

The built-in ROS 2 to Rerun type mappings.

Rewire maps these ROS 2 message types to Rerun archetypes automatically. No configuration needed.

Rewire aims to align its mappings with the conventions Rerun uses for MCAP ingestion, so data recorded with Rewire should look familiar if you've used Rerun's built-in MCAP loader. That said, full parity is not guaranteed — some mappings may differ where Rewire optimizes for live streaming or supports ROS 2-specific semantics that don't have a direct MCAP equivalent.

You can always check the latest list by running `rewire types`.

Some types log their reported uncertainty as an extra `Scalars` series alongside the measurement:

- `Temperature`, `FluidPressure`, `RelativeHumidity`, `Illuminance`, `Range` — variance
- `Imu` — orientation, angular velocity and linear acceleration covariance diagonals
- `Odometry` — twist covariance diagonal

## [example\_interfaces](https://docs.rewire.run/supported-types#example_interfaces)

Newer ROS 2 distributions publish their demo topics with these types instead of the deprecated `std_msgs` primitives. They are wire-identical, and Rewire maps them the same way.

| Message | Rerun Archetype |
| --- | --- |
| `Bool` | `Scalars` |
| `Byte` | `Scalars` |
| `Char` | `Scalars` |
| `Float32` | `Scalars` |
| `Float64` | `Scalars` |
| `Int8` | `Scalars` |
| `Int16` | `Scalars` |
| `Int32` | `Scalars` |
| `Int64` | `Scalars` |
| `String` | `TextDocument` |
| `UInt8` | `Scalars` |
| `UInt16` | `Scalars` |
| `UInt32` | `Scalars` |
| `UInt64` | `Scalars` |

## [geometry\_msgs](https://docs.rewire.run/supported-types#geometry_msgs)

| Message | Rerun Archetype |
| --- | --- |
| `AccelStamped` | `Scalars` |
| `AccelWithCovarianceStamped` | `Scalars` |
| `PointStamped` | `Points3D` |
| `PolygonStamped` | `LineStrips3D` |
| `PoseArray` | `Points3D` |
| `PoseStamped` | `InstancePoses3D`, `Scalars` |
| `PoseWithCovarianceStamped` | `InstancePoses3D`, `Ellipsoids3D`, `Scalars` |
| `QuaternionStamped` | `Scalars` |
| `Twist` | `Scalars` |
| `TwistStamped` | `Scalars` |
| `TwistWithCovariance` | `Scalars` |
| `TwistWithCovarianceStamped` | `Scalars` |
| `Vector3Stamped` | `Scalars` |
| `VelocityStamped` | `Scalars` |
| `WrenchStamped` | `Scalars` |

## [geographic\_msgs](https://docs.rewire.run/supported-types#geographic_msgs)

| Message | Rerun Archetype |
| --- | --- |
| `GeoPoseStamped` | `GeoPoints`, `Scalars` |
| `GeoPoseWithCovarianceStamped` | `GeoPoints`, `Scalars` |
| `GeoPath` | `GeoLineStrings` |

## [nav\_msgs](https://docs.rewire.run/supported-types#nav_msgs)

| Message | Rerun Archetype |
| --- | --- |
| `GridCells` | `Points3D` |
| `OccupancyGrid` | `GridMap` |
| `Odometry` | `InstancePoses3D`, `Ellipsoids3D`, `Scalars` |
| `Path` | `LineStrips3D` |

## [nav2\_msgs](https://docs.rewire.run/supported-types#nav2_msgs)

| Message | Rerun Archetype |
| --- | --- |
| `VoxelGrid` | `VoxelGridMap` |

The `VoxelGrid` converter is marked unstable while Rerun's `VoxelGridMap` archetype is itself experimental. Occupied voxels are colored by value through the Turbo colormap.

## [rcl\_interfaces](https://docs.rewire.run/supported-types#rcl_interfaces)

| Message | Rerun Archetype |
| --- | --- |
| `Log` | `TextLog` |

## [sensor\_msgs](https://docs.rewire.run/supported-types#sensor_msgs)

| Message | Rerun Archetype |
| --- | --- |
| `BatteryState` | `Scalars` |
| `CameraInfo` | `Pinhole` |
| `CompressedImage` | `EncodedImage`, `EncodedDepthImage`, `VideoStream` (h264 and h265 streams) |
| `FluidPressure` | `Scalars` |
| `Illuminance` | `Scalars` |
| `Image` | `Image`, `DepthImage` |
| `Imu` | `Scalars` |
| `JointState` | `Scalars` |
| `Joy` | `Scalars` |
| `LaserScan` | `Points3D` |
| `MagneticField` | `Arrows3D` |
| `NavSatFix` | `GeoPoints` |
| `PointCloud2` | `Points3D` (renders `intensity`, `rgba`, and `label` fields when present; supports `INT64`, `UINT64`, and `BOOL` field datatypes) |
| `Range` | `Scalars` |
| `RelativeHumidity` | `Scalars` |
| `Temperature` | `Scalars` |

## [std\_msgs](https://docs.rewire.run/supported-types#std_msgs)

| Message | Rerun Archetype |
| --- | --- |
| `Bool` | `Scalars` |
| `Byte` | `Scalars` |
| `Char` | `Scalars` |
| `Float32` | `Scalars` |
| `Float64` | `Scalars` |
| `Int8` | `Scalars` |
| `Int16` | `Scalars` |
| `Int32` | `Scalars` |
| `Int64` | `Scalars` |
| `String` | `TextDocument` |
| `UInt8` | `Scalars` |
| `UInt16` | `Scalars` |
| `UInt32` | `Scalars` |
| `UInt64` | `Scalars` |

`String` on a topic ending in `/robot_description` is loaded as a URDF instead, which produces `Asset3D`, `Transform3D`, `CoordinateFrame` and `InstancePoses3D`. See [URDF Loading](https://docs.rewire.run/urdf-tf/urdf).

## [tf2\_msgs](https://docs.rewire.run/supported-types#tf2_msgs)

| Message | Rerun Archetype |
| --- | --- |
| `TFMessage` | `Transform3D` |

## [vision\_msgs](https://docs.rewire.run/supported-types#vision_msgs)

| Message | Rerun Archetype |
| --- | --- |
| `Detection2DArray` | `Boxes2D` |
| `Detection3DArray` | `Boxes3D` |

## [visualization\_msgs](https://docs.rewire.run/supported-types#visualization_msgs)

| Message | Rerun Archetype |
| --- | --- |
| `ImageMarker` | `Points2D`, `LineStrips2D`, `Clear` |
| `Marker` | `Arrows3D`, `Boxes3D`, `Ellipsoids3D`, `Capsules3D`, `LineStrips3D`, `Points3D`, `Mesh3D`, `Clear` |
| `MarkerArray` | Same as `Marker` |

[Examples\\ \\ Example custom mapping configurations.](https://docs.rewire.run/custom-mappings/examples) [URDF Loading\\ \\ Automatic robot model visualization from /robot\_description.](https://docs.rewire.run/urdf-tf/urdf)

### On this page

[example\_interfaces](https://docs.rewire.run/supported-types#example_interfaces) [geometry\_msgs](https://docs.rewire.run/supported-types#geometry_msgs) [geographic\_msgs](https://docs.rewire.run/supported-types#geographic_msgs) [nav\_msgs](https://docs.rewire.run/supported-types#nav_msgs) [nav2\_msgs](https://docs.rewire.run/supported-types#nav2_msgs) [rcl\_interfaces](https://docs.rewire.run/supported-types#rcl_interfaces) [sensor\_msgs](https://docs.rewire.run/supported-types#sensor_msgs) [std\_msgs](https://docs.rewire.run/supported-types#std_msgs) [tf2\_msgs](https://docs.rewire.run/supported-types#tf2_msgs) [vision\_msgs](https://docs.rewire.run/supported-types#vision_msgs) [visualization\_msgs](https://docs.rewire.run/supported-types#visualization_msgs)

Last updated on August 26, 2026

© 2026 Rewire