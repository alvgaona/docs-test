# Source: https://docs.rewire.run/

# Introduction

Rewire is a drop-in ROS 2 bridge for Rerun. Stream live topics to the viewer in seconds.

Rewire connects to your ROS 2 network via DDS and Zenoh, discovers topics automatically, and streams them to [Rerun](https://rerun.io) for real-time visualization. The bundled [Rewire Viewer](https://docs.rewire.run/viewer) is a thin extension of the official Rerun viewer that adds ROS 2 panels (Topics, Nodes, Diagnostics) — but Rewire streams to any Rerun viewer just as well.

It's a single Rust binary — no `rclcpp`, no `setup.bash`, no colcon workspaces.

The one exception: URDF mesh resolution. If you visualize a robot model with `package://` mesh URIs, source your ROS 2 environment so `AMENT_PREFIX_PATH` is set before running Rewire.

## [From zero to viewer in two commands](https://docs.rewire.run/#from-zero-to-viewer-in-two-commands)

### [Install](https://docs.rewire.run/#install)

```
curl -fsSL https://rewire.run/install.sh | sh
```

The script installs both `rewire` (the bridge) and `rewire-viewer`. See [other install methods](https://docs.rewire.run/getting-started/installation) if you prefer pixi, APT or Nix.

### [Run](https://docs.rewire.run/#run)

```
rewire record
```

The Rewire Viewer launches and starts receiving every topic discovered on your network. To stream into a Rerun viewer you already have running on another machine, use `--connect host:9876` instead.

## [Where to next?](https://docs.rewire.run/#where-to-next)

[**Quick Start**\\ \\ Subscribe to specific topics, save recordings, stream to a remote viewer, enable diagnostics.](https://docs.rewire.run/getting-started/quick-start) [**First Run**\\ \\ What Rewire does the first time you run it, plus environment checks.](https://docs.rewire.run/getting-started/first-run) [**Architecture**\\ \\ Why Rewire isn't a ROS 2 node and how it observes the network passively.](https://docs.rewire.run/concepts/architecture) [**Custom Mappings**\\ \\ Map any ROS 2 type to Rerun archetypes via JSON5 config — no code required.](https://docs.rewire.run/custom-mappings/overview)

[Installation\\ \\ Install Rewire on macOS or Linux.](https://docs.rewire.run/getting-started/installation)

### On this page

[From zero to viewer in two commands](https://docs.rewire.run/#from-zero-to-viewer-in-two-commands) [Install](https://docs.rewire.run/#install) [Run](https://docs.rewire.run/#run) [Where to next?](https://docs.rewire.run/#where-to-next)

Last updated on August 26, 2026

© 2026 Rewire