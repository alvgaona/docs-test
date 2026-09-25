# Source: https://docs.rewire.run/getting-started/first-run

Getting Started

# First Run

What to expect when you run Rewire for the first time.

## [What happens](https://docs.rewire.run/getting-started/first-run#what-happens)

When you run `rewire record`, Rewire walks through the following sequence:

### [Start the relay and launch the viewer](https://docs.rewire.run/getting-started/first-run#start-the-relay-and-launch-the-viewer)

Rewire starts an in-process [relay](https://docs.rewire.run/relay) on port `9876` and spawns the [Rewire Viewer](https://docs.rewire.run/viewer) next to the bridge process, connected through it. The viewer can restart without interrupting the recording.

### [Discover the network](https://docs.rewire.run/getting-started/first-run#discover-the-network)

Rewire creates a DDS and Zenoh context to discover topics on the network and waits for topic discovery to settle (default: 5 seconds).

### [Subscribe and stream](https://docs.rewire.run/getting-started/first-run#subscribe-and-stream)

Matching topics are subscribed automatically and data starts flowing to the viewer in real time.

### [Track changes](https://docs.rewire.run/getting-started/first-run#track-changes)

New topics are picked up as they appear, and Rewire unsubscribes when topics go away.

![Rewire Viewer with ROS 2 data](https://docs.rewire.run/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fviewer-overview.f03a53dc.png&w=3840&q=75)

Rewire is not a ROS 2 node — it observes the DDS/Zenoh network directly and never interferes with your ROS graph.

## [Environment check](https://docs.rewire.run/getting-started/first-run#environment-check)

Run the health check to verify your environment:

```
rewire doctor
```

This checks for middleware availability, network configuration, and converter readiness.

## [Shell completions](https://docs.rewire.run/getting-started/first-run#shell-completions)

Generate completions for your shell:

bashzshfish

```
rewire completions bash > ~/.bash_completion.d/rewire
```

```
rewire completions zsh > ~/.zfunc/_rewire
```

```
rewire completions fish > ~/.config/fish/completions/rewire.fish
```

## [List supported types](https://docs.rewire.run/getting-started/first-run#list-supported-types)

See all built-in type mappings:

```
rewire types
```

This shows every supported ROS 2 message type and the corresponding Rerun archetype.

[Quick Start\\ \\ Stream your first ROS 2 topics to Rerun in under a minute.](https://docs.rewire.run/getting-started/quick-start) [Rewire Viewer\\ \\ The custom Rerun-based viewer built for ROS 2 workflows.](https://docs.rewire.run/viewer)

### On this page

[What happens](https://docs.rewire.run/getting-started/first-run#what-happens) [Start the relay and launch the viewer](https://docs.rewire.run/getting-started/first-run#start-the-relay-and-launch-the-viewer) [Discover the network](https://docs.rewire.run/getting-started/first-run#discover-the-network) [Subscribe and stream](https://docs.rewire.run/getting-started/first-run#subscribe-and-stream) [Track changes](https://docs.rewire.run/getting-started/first-run#track-changes) [Environment check](https://docs.rewire.run/getting-started/first-run#environment-check) [Shell completions](https://docs.rewire.run/getting-started/first-run#shell-completions) [List supported types](https://docs.rewire.run/getting-started/first-run#list-supported-types)

Last updated on August 26, 2026

© 2026 Rewire