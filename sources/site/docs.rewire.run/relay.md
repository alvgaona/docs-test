# Source: https://docs.rewire.run/relay

# Relay

A headless hub between bridges and viewers, with automatic reconnect and archiving.

The relay is a headless hub that sits between bridges and viewers. Bridges stream into it, viewers connect to it, and either side can restart without taking the other down. A viewer that joins mid-session goes live immediately, and the bridge replays the scene — static data, latched topics — to each reader that joins, so a late viewer still gets its transform tree and models.

You rarely have to think about it. The `rewire record` flow embeds a relay automatically. Run one explicitly with `rewire serve` when you want a long-lived hub — on a robot, a lab server, or anywhere viewers come and go.

## [Embedded relay (the default)](https://docs.rewire.run/relay#embedded-relay-the-default)

When you run `rewire record` with no flags and port `9876` is free, the bridge starts an in-process relay and connects the bundled [Rewire Viewer](https://docs.rewire.run/viewer) through it. Closing or restarting the viewer no longer interrupts recording — reopen a viewer and it picks up the buffered stream.

When the port is already busy, the bridge probes what is listening and adapts. It uses an existing relay as-is, detects a legacy 0.5.x `rewire-viewer` via its service port, and treats anything else as a stock Rerun viewer.

A `rewire record --save` run resolves the same live half, so an archive is written and streamed at once. Pass `--no-live` to skip the probe and the spawn entirely and write only the file.

## [Standalone relay: `rewire serve`](https://docs.rewire.run/relay#standalone-relay-rewire-serve)

```
rewire serve
```

Bridges connect to it with `--connect`, from the same machine or across the network:

```
rewire record --connect relay-host:9876
```

| Flag | Description | Default |
| --- | --- | --- |
| `--bind <ADDR>` | Address to bind the gRPC listener on | `0.0.0.0` |
| `--port <PORT>` | gRPC listen port | `9876` |
| `--memory-limit <LIMIT>` | Buffered history limit before the oldest data is dropped (`64MiB`, `500MB`, `50%`, `unlimited`) | `1MiB` |
| `--cors-allowed-origin <PATTERN>` | Additional origin allowed for cross-origin gRPC-web requests (repeatable) | — |
| `--save [STEM]` | Archive everything the relay receives to a `.rrd` file | — |
| `--config <PATH>` | Load JSON5 config file (`serve` section) | `~/.config/rewire/config.json5` |

All flags can also be set in the [`serve` section](https://docs.rewire.run/configuration/json5-config#serve) of the config file; flags take precedence.

## [Buffered history](https://docs.rewire.run/relay#buffered-history)

The relay keeps essentially no temporal history. At the `1MiB` default, readers are served the present rather than a backfill of the past.

That is deliberate. A backfill is sent at wire speed on top of the real-time stream, which a live viewer cannot absorb — it parks unread in the viewer's receive queue and shows up as staleness that never clears, scaling with the size of the buffer. A reconnecting viewer gets its scene from the bridge's replay on join, not from relay history, so the backfill costs latency and buys nothing.

Static and store-info records are evicted last, so the scene survives even at this size for topologies with no replaying bridge in front of the relay. Raise `--memory-limit` if you specifically want deep late-join backfill from the relay itself, and expect a proportional stall in a viewer that joins mid-session.

This changed in v0.9.0. Earlier versions defaulted to `512MiB` of history.

## [Automatic reconnect](https://docs.rewire.run/relay#automatic-reconnect)

Bridges supervise their connection to the relay (and viewers supervise theirs). When a relay or viewer restarts, the bridge reconnects on its own and keeps streaming the same recording — no restart, no manual intervention.

Reconnecting alone is not enough for ROS 2 data. Latched topics like `/tf_static` are published once, and their publishers are often gone by the time a viewer rejoins. Rewire replays them after every reconnect, along with one-time static data such as coordinate frames and URDF models, so a restarted relay or a late viewer still gets a complete transform tree.

The same replay covers viewers that simply join late. A latched message published once at startup can be pushed out of the relay's buffer by hours of high-rate traffic, so the bridge watches for readers joining the relay and replays its latched cache to each of them, at the original times. Late joiners get their transforms and models no matter how long the session has been running.

Data logged while the relay was unreachable is not re-sent to the live stream. For a gapless record of a session, combine `--connect` with `--save` — the local archive keeps writing through any outage.

## [Archiving with `--save`](https://docs.rewire.run/relay#archiving-with---save)

The relay can archive everything it receives into a single `.rrd` file, finalized on shutdown:

```
rewire serve --save flight-test        # → flight-test_<timestamp>.rrd
rewire serve --save                    # → rewire-relay_<timestamp>.rrd
```

You name a stem and Rewire completes the filename with the session timestamp, so restarting the relay never overwrites an earlier archive. There is no rotation or retention — one session, one file. For per-recording archives written by the bridge itself, see [`rewire record --save`](https://docs.rewire.run/cli-reference#rewire-record).

## [Monitoring with `rewire status`](https://docs.rewire.run/relay#monitoring-with-rewire-status)

Query any relay for its identity, uptime, buffer memory, and connected bridges:

```
rewire status                          # queries 127.0.0.1:9876
rewire status --connect relay-host     # or host:port
```

Each connected bridge is listed with its state, application ID, active recording ID, and how recently it was heard from. Recordings started with [`rewire log`](https://docs.rewire.run/concepts/runtime-control) show up within a heartbeat.

## [Browser viewers](https://docs.rewire.run/relay#browser-viewers)

The relay speaks gRPC-web and sets CORS headers, so a web build of the Rerun viewer can connect to it straight from a browser. Same-origin and localhost work out of the box; allow additional origins explicitly:

```
rewire serve --cors-allowed-origin "https://*.example.com"
```

## [Multi-bridge topologies](https://docs.rewire.run/relay#multi-bridge-topologies)

A single relay can fan in any number of bridges and fan out to any number of viewers. Point every robot at the same relay and open viewers wherever you need them:

```
# On each robot
rewire record --connect relay-host:9876

# Anywhere
rewire status --connect relay-host
```

The [Topics and Nodes panels](https://docs.rewire.run/viewer#custom-panels) aggregate data from all connected bridges.

[Rewire Viewer\\ \\ The custom Rerun-based viewer built for ROS 2 workflows.](https://docs.rewire.run/viewer) [Architecture\\ \\ How Rewire connects to your ROS 2 network without being a ROS 2 node.](https://docs.rewire.run/concepts/architecture)

### On this page

[Embedded relay (the default)](https://docs.rewire.run/relay#embedded-relay-the-default) [Standalone relay: `rewire serve`](https://docs.rewire.run/relay#standalone-relay-rewire-serve) [Buffered history](https://docs.rewire.run/relay#buffered-history) [Automatic reconnect](https://docs.rewire.run/relay#automatic-reconnect) [Archiving with `--save`](https://docs.rewire.run/relay#archiving-with---save) [Monitoring with `rewire status`](https://docs.rewire.run/relay#monitoring-with-rewire-status) [Browser viewers](https://docs.rewire.run/relay#browser-viewers) [Multi-bridge topologies](https://docs.rewire.run/relay#multi-bridge-topologies)

Last updated on August 26, 2026

© 2026 Rewire