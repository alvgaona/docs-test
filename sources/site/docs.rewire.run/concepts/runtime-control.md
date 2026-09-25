# Source: https://docs.rewire.run/concepts/runtime-control

# Runtime Control

Control a running Rewire instance without restarting it.

Rewire supports runtime control over a loopback gRPC service. You can start new recordings and inspect the topics a running bridge is subscribed to — all without restarting it.

## [How it works](https://docs.rewire.run/concepts/runtime-control#how-it-works)

When `rewire record` starts, it hosts a gRPC control service on an OS-assigned port on `127.0.0.1` and registers itself by writing a small per-process file (`rewire-<pid>.json`) to the runtime directory. CLI commands such as `rewire log` and `rewire topics` read that registry to discover the live bridge and dial its endpoint.

Earlier versions used a Unix domain socket for this. As of v0.4.0, runtime control runs over loopback gRPC instead — the Unix-socket IPC has been removed.

## [Starting a new recording](https://docs.rewire.run/concepts/runtime-control#starting-a-new-recording)

```
rewire log
```

This tells the running Rewire instance to start a new recording. The viewer opens a new recording session while the bridge keeps streaming — no data loss, no reconnection. Recordings started this way behave exactly like the startup recording — they survive [relay](https://docs.rewire.run/relay) and viewer restarts, and `rewire status` reports whichever recording is live.

This is useful when you want to segment recordings by experiment, test run, or scenario without interrupting the data flow.

If several bridges are running, target a specific one with `--pid`:

```
rewire log --pid 12345
```

## [Use cases](https://docs.rewire.run/concepts/runtime-control#use-cases)

- **Experiment segmentation** — start a new recording for each test run
- **Continuous monitoring** — keep Rewire running and trigger recordings on demand
- **CI/CD integration** — script recording management in automated test pipelines

## [Requirements](https://docs.rewire.run/concepts/runtime-control#requirements)

Runtime control requires both the running `rewire record` instance and the client command to be on the same machine: the gRPC service binds to loopback (`127.0.0.1`) only.

The `rewire status` command is different — it queries a [relay](https://docs.rewire.run/relay) over the network rather than a local bridge, so it works from any machine that can reach the relay port.

[Transport Selection\\ \\ Choosing between DDS and Zenoh for your ROS 2 network.](https://docs.rewire.run/transport-selection) [Bag Playback\\ \\ Stream a recorded MCAP bag to a viewer, or convert it to an RRD file.](https://docs.rewire.run/playback)

### On this page

[How it works](https://docs.rewire.run/concepts/runtime-control#how-it-works) [Starting a new recording](https://docs.rewire.run/concepts/runtime-control#starting-a-new-recording) [Use cases](https://docs.rewire.run/concepts/runtime-control#use-cases) [Requirements](https://docs.rewire.run/concepts/runtime-control#requirements)

Last updated on August 26, 2026

© 2026 Rewire