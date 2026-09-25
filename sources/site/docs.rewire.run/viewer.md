# Source: https://docs.rewire.run/viewer

# Rewire Viewer

The custom Rerun-based viewer built for ROS 2 workflows.

Rewire ships with its own viewer — a custom build of Rerun with panels designed specifically for ROS 2 development. It launches automatically when you run `rewire record`.

![Rewire Viewer overview](https://docs.rewire.run/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fviewer-overview.f03a53dc.png&w=3840&q=75)

## [Auto-spawn](https://docs.rewire.run/viewer#auto-spawn)

When you run `rewire record` without a `--connect` flag, Rewire starts an in-process [relay](https://docs.rewire.run/relay), looks for the `rewire-viewer` binary next to its own executable or on your `PATH`, and launches it connected through the relay. No extra steps needed.

```
rewire record
```

The viewer opens and starts receiving data immediately. Because the viewer connects through the relay, you can close and reopen it — or it can crash — without interrupting the recording. When the bridge shuts down, the viewer stays open so you can continue inspecting the recording.

If the default port is already in use, Rewire probes what is listening and connects to it instead — first a running relay, then a legacy 0.5.x `rewire-viewer`, and finally a stock Rerun viewer. If `rewire-viewer` is not found (e.g. a manual install of only the `rewire` binary), Rewire falls back to the official Rerun viewer.

## [Custom panels](https://docs.rewire.run/viewer#custom-panels)

The Rewire Viewer adds three panels on top of the standard Rerun interface.

### [Topics](https://docs.rewire.run/viewer#topics)

![Topics panel](https://docs.rewire.run/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fviewer-topics.45eccb52.png&w=3840&q=75)

A sortable table of all subscribed topics showing:

- Topic name and message type
- Publisher and subscriber counts

Topics update in real time as they are discovered, subscribed, or unsubscribed.

### [Nodes](https://docs.rewire.run/viewer#nodes)

![Nodes panel](https://docs.rewire.run/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fviewer-nodes.d5ce446f.png&w=3840&q=75)

Discovered ROS 2 nodes that publish or subscribe to actively bridged topics. Shows node names, publisher/subscriber counts, and transport protocol (DDS or Zenoh).

### [Diagnostics](https://docs.rewire.run/viewer#diagnostics)

![Diagnostics panel](https://docs.rewire.run/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fviewer-diagnostics.1e0562ea.png&w=3840&q=75)

Per-topic diagnostics at a glance:

- Hz (actual vs expected)
- Bandwidth
- Dropped messages
- Latency

This panel complements the per-topic diagnostic time series that Rewire logs as Rerun `Scalars` when `--diagnostics` is enabled.

## [Using a remote viewer](https://docs.rewire.run/viewer#using-a-remote-viewer)

To stream data to a viewer running on another machine, use `--connect`:

```
rewire record --connect 192.168.1.50:9876
```

Shorthand forms are accepted:

| Input | Resolved to |
| --- | --- |
| `192.168.1.50:9876` | `rerun+http://192.168.1.50:9876/proxy` |
| `192.168.1.50` | `rerun+http://192.168.1.50:9876/proxy` |
| `localhost` | `rerun+http://127.0.0.1:9876/proxy` |

When `--connect` is specified, Rewire does not auto-spawn a local viewer.

## [Multi-bridge fleet visualization](https://docs.rewire.run/viewer#multi-bridge-fleet-visualization)

Multiple bridge instances can stream to the same viewer simultaneously. Each bridge reports a unique heartbeat, and the viewer tracks connection status and bridge count automatically.

```
# Robot 1
rewire record --connect viewer-host:9876

# Robot 2 (different machine, same viewer)
rewire record --connect viewer-host:9876
```

The Topics and Nodes panels aggregate data from all connected bridges. For fleets, run a standalone [relay](https://docs.rewire.run/relay) as the hub instead of a viewer — bridges reconnect to it automatically, and viewers can come and go without disturbing the streams.

## [Ports](https://docs.rewire.run/viewer#ports)

In the default flow, the embedded [relay](https://docs.rewire.run/relay) hosts both the Rerun data stream and the Rewire service (heartbeats, info) on a single port, `9876`, and the viewer connects to it as a client.

When the viewer runs standalone in the 0.5.x style — receiving streams directly, without a relay — it uses two ports:

| Port | Protocol | Purpose |
| --- | --- | --- |
| 9876 | gRPC | Rerun data stream |
| 9877 | gRPC | Viewer service (heartbeat, info) |

Both ports are configurable via the `rewire-viewer` CLI:

```
rewire-viewer --port 9876 --grpc-port 9877
```

The service port defaults to data port + 1 when not specified.

[First Run\\ \\ What to expect when you run Rewire for the first time.](https://docs.rewire.run/getting-started/first-run) [Relay\\ \\ A headless hub between bridges and viewers, with automatic reconnect and archiving.](https://docs.rewire.run/relay)

### On this page

[Auto-spawn](https://docs.rewire.run/viewer#auto-spawn) [Custom panels](https://docs.rewire.run/viewer#custom-panels) [Topics](https://docs.rewire.run/viewer#topics) [Nodes](https://docs.rewire.run/viewer#nodes) [Diagnostics](https://docs.rewire.run/viewer#diagnostics) [Using a remote viewer](https://docs.rewire.run/viewer#using-a-remote-viewer) [Multi-bridge fleet visualization](https://docs.rewire.run/viewer#multi-bridge-fleet-visualization) [Ports](https://docs.rewire.run/viewer#ports)

Last updated on August 26, 2026

© 2026 Rewire