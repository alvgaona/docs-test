# Source: https://docs.rewire.run/getting-started/quick-start

Getting Started

# Quick Start

Stream your first ROS 2 topics to Rerun in under a minute.

With a ROS 2 system running, pick the workflow that matches what you want to do:

All topicsSpecific topicsExclude topicsRemote viewerSave to fileHeadless relayDiagnostics

Stream every discovered topic and launch the [Rewire Viewer](https://docs.rewire.run/viewer):

```
rewire record
```

Only subscribe to a curated list of topics:

```
rewire record --topics /camera/image /scan /tf
```

Subscribe to everything except matching glob patterns:

```
rewire record --exclude "/diagnostics/*" "/rosout" "/parameter_events"
```

Connect to a Rerun viewer running on another machine:

```
rewire record --connect 192.168.1.100:9876
```

Save the session to an `.rrd` file for later playback:

```
rewire record --save recording
```

The `--save` flag takes a filename stem, and Rewire completes it with the recording ID — the example above writes `recording_<recording_id>.rrd`. Use `--save-session` to append every recording of the run to a single file instead.

A save run also streams, so the viewer comes up and you can watch what you are capturing. Add `--no-live` to write only the file — no viewer, no relay — for measurement runs and machines with no display.

Point the live half at a specific address by combining `--save` with `--connect`. The local archive keeps writing even if the [relay](https://docs.rewire.run/relay) or viewer restarts.

Run a standalone [relay](https://docs.rewire.run/relay) as a long-lived hub, then point bridges and viewers at it:

```
# Start the hub (e.g. on a robot or lab server)
rewire serve

# Stream to it from anywhere on the network
rewire record --connect relay-host:9876

# Check who's connected
rewire status --connect relay-host
```

Monitor per-topic Hz, bandwidth, drops, and latency:

```
rewire record --diagnostics
```

Diagnostics are logged as Rerun `Scalars` entities under `/rewire/bridge/topics/{topic_name}/`.

[Installation\\ \\ Install Rewire on macOS or Linux.](https://docs.rewire.run/getting-started/installation) [First Run\\ \\ What to expect when you run Rewire for the first time.](https://docs.rewire.run/getting-started/first-run)

### On this page

No Headings

Last updated on August 26, 2026

© 2026 Rewire