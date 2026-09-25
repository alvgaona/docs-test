# Source: https://docs.rewire.run/concepts/architecture

# Architecture

How Rewire connects to your ROS 2 network without being a ROS 2 node.

Rewire is a standalone observer that sits alongside your ROS 2 network. It is **not** a ROS 2 node — it does not use `rclcpp`, does not appear in the ROS graph, and does not interfere with your existing nodes.

## [Passive observer](https://docs.rewire.run/concepts/architecture#passive-observer)

Rewire joins the network at the middleware layer, listening to DDS and Zenoh traffic directly. Your nodes never know it's there. This means:

- No extra load on the ROS graph
- No additional discovery overhead for your nodes
- No risk of interfering with node lifecycles or QoS negotiations
- Works with any ROS 2 distribution (Humble, Iron, Jazzy, Kilted, Lyrical, Rolling)

## [DDS and Zenoh](https://docs.rewire.run/concepts/architecture#dds-and-zenoh)

Rewire speaks both DDS and Zenoh natively and simultaneously. When you run `rewire record`, it discovers topics on both transports and subscribes to whichever transport each topic is available on.

If your network uses the default DDS middleware (CycloneDDS, FastDDS, etc.), Rewire connects via DDS. If you're running Zenoh-based ROS 2 (via `rmw_zenoh`), Rewire connects via Zenoh. Mixed networks work too — Rewire handles both at the same time.

## [Data pipeline](https://docs.rewire.run/concepts/architecture#data-pipeline)

1. **Subscribe** — Rewire subscribes to topics on DDS/Zenoh
2. **Deserialize** — Raw messages are deserialized using the topic's type information
3. **Convert** — Each message is mapped to a Rerun archetype (e.g., `sensor_msgs/Image` becomes `Image`)
4. **Stream** — Converted data is streamed through the [relay](https://docs.rewire.run/relay) to the [Rewire Viewer](https://docs.rewire.run/viewer) over gRPC

Each step happens per-message with minimal buffering. Data flows from network to viewer in real time. In the default flow the relay is embedded in the bridge and hosts both the data stream and the Rewire service (heartbeats, metadata) on port 9876; a standalone `rewire serve` relay does the same. Viewers connect to the relay as clients, so either side can restart without disturbing the other.

## [No ROS 2 install required](https://docs.rewire.run/concepts/architecture#no-ros-2-install-required)

Because Rewire implements the middleware protocols directly, it does not depend on any ROS 2 libraries. The binary is fully self-contained — no `setup.bash`, no `LD_LIBRARY_PATH`, no `ament` packages (unless you need mesh resolution for URDF models).

Type resolution for custom messages happens automatically via ROS 2 service introspection. For pre-Jazzy nodes that don't support this, you may still need to source your workspace so Rewire can find type definitions for deserialization.

## [Two binaries](https://docs.rewire.run/concepts/architecture#two-binaries)

Rewire ships as two static binaries with no runtime dependencies:

- **`rewire`** — the bridge that connects to your ROS 2 network
- **`rewire-viewer`** — the custom Rerun-based viewer with ROS 2 panels

Both work on macOS and Linux across x86\_64 and aarch64 architectures. Install them, run `rewire record`, and the viewer launches automatically.

[Relay\\ \\ A headless hub between bridges and viewers, with automatic reconnect and archiving.](https://docs.rewire.run/relay) [Entity Mapping\\ \\ How ROS 2 topics become Rerun entities in the viewer.](https://docs.rewire.run/concepts/entity-mapping)

### On this page

[Passive observer](https://docs.rewire.run/concepts/architecture#passive-observer) [DDS and Zenoh](https://docs.rewire.run/concepts/architecture#dds-and-zenoh) [Data pipeline](https://docs.rewire.run/concepts/architecture#data-pipeline) [No ROS 2 install required](https://docs.rewire.run/concepts/architecture#no-ros-2-install-required) [Two binaries](https://docs.rewire.run/concepts/architecture#two-binaries)

Last updated on August 26, 2026

© 2026 Rewire