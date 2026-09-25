# Source: https://docs.rewire.run/configuration/json5-config

Configuration

# JSON5 Config

Configure Rewire with a JSON5 configuration file.

Rewire loads its configuration from a JSON5 file.

Default locationCustom path

By default Rewire looks at:

```
~/.config/rewire/config.json5
```

Specify any path with `--config`:

```
rewire record --config /path/to/config.json5
```

All fields below are optional. CLI flags take precedence over config file values. Run `rewire config generate` to create a starter config with all fields documented.

## [Full example](https://docs.rewire.run/configuration/json5-config#full-example)

```
{
    app_id: "my-robot",
    transport: "all",
    domain_id: 42,
    queue_depth: 16,
    log_level: "rewire=debug",
    diagnostics: true,
    latched_history_depth: 8,
    output: {
        connect: "127.0.0.1:9870",
        save: "/tmp/recording",
        save_session: false,
    },
    serve: {
        bind: "0.0.0.0",
        port: 9876,
        memory_limit: "1MiB",
        cors_allowed_origins: ["https://*.example.com"],
        save: "/tmp/relay-archive",
    },
    topics: {
        include: ["/camera/*", "/odom"],
        exclude: ["/rosout", "/parameter_events"],
    },
    topic_overrides: [
        { name: "/camera/image", max_hz: 10 },
        { name: "/diagnostics", enabled: false },
    ],
    ws: {
        port: 9090,
        auth_token: "your-secret-token",
    },
    zenoh: {
        connect: ["tcp/192.168.1.100:7447"],
    },
}
```

## [Fields](https://docs.rewire.run/configuration/json5-config#fields)

### [`app_id`](https://docs.rewire.run/configuration/json5-config#app_id)

Application ID for the Rerun recording. Defaults to `"rewire"`.

### [`transport`](https://docs.rewire.run/configuration/json5-config#transport)

Which wire transports to start: `"all"`, `"dds"` or `"zenoh"`. Default: `"all"`, which starts both. Overridden by `--transport`, and shared by `rewire record`, `rewire doctor` and `rewire mappings generate`. See [Transport Selection](https://docs.rewire.run/transport-selection#choosing-transports).

### [`domain_id`](https://docs.rewire.run/configuration/json5-config#domain_id)

ROS 2 domain ID. Defaults to the `ROS_DOMAIN_ID` environment variable, or `0`.

### [`discovery_timeout`](https://docs.rewire.run/configuration/json5-config#discovery_timeout)

Seconds to block on initial topic discovery before subscribing. Unset by default: recording starts immediately and topics are bridged as they are discovered. Set it only if you want the bridge to wait for the graph to settle first.

### [`queue_depth`](https://docs.rewire.run/configuration/json5-config#queue_depth)

Message queue depth per subscription. Default varies by QoS profile.

### [`latched_history_depth`](https://docs.rewire.run/configuration/json5-config#latched_history_depth)

History depth for latched (transient-local) topics like `/tf_static`, equivalent to the flag `--latched-depth`. Raise it when several sources publish on the same latched topic and some of their messages are missed.

### [`log_level`](https://docs.rewire.run/configuration/json5-config#log_level)

Tracing filter string controlling terminal verbosity. Examples: `"info"`, `"rewire=debug"`. The `RUST_LOG` environment variable takes precedence when set. The default shows info-level output from Rewire itself and warnings from everything else.

This filter only affects the terminal. Rewire always keeps a debug-level log file on disk — see [Where does Rewire write its logs?](https://docs.rewire.run/faq#log-files) for the location and retention policy.

### [`diagnostics`](https://docs.rewire.run/configuration/json5-config#diagnostics)

Enable per-topic diagnostics (Hz, bandwidth, drops, latency). Default: `false`.

### [`output`](https://docs.rewire.run/configuration/json5-config#output)

| Field | Description |
| --- | --- |
| `connect` | Address of a remote Rerun viewer or [relay](https://docs.rewire.run/relay) (`host:port`) |
| `save` | Filename stem for archives; each recording is written to `<stem>_<recording_id>.rrd` (a `.rrd` suffix on the stem is ignored) |
| `live` | Whether a `save` run keeps its live half. `false` records file-only — no port probe, no connect, no viewer spawn — matching `--no-live`. Requires `save`, conflicts with `connect`. Default: `true` |
| `save_session` | With `save`, append every recording of the run to one `<stem>_<timestamp>.rrd` instead of one file per recording |
| `flush_tick_ms` | How often the live stream is flushed to the viewer, in milliseconds. Lower values shave display latency but send the viewer more, smaller batches than it can ingest. Ignored when `live` is `false`. Default: `8` |
| `scalar_flush_tick_ms` | Hold plot-class chunks (scalars, series styling, text logs) this many milliseconds and forward them merged into one chunk per entity. Plots look identical while chunk rate drops by an order of magnitude; `0` disables coalescing. Default: `150` |
| `live_queue_mib` | Byte budget in MiB for the live stream's never-blocking queue. A slow or dead consumer costs counted drops of the oldest data instead of backpressure and a stale replay on recovery; `0` disables the bound. Default: `64` |

### [`topics`](https://docs.rewire.run/configuration/json5-config#topics)

| Field | Description |
| --- | --- |
| `include` | Glob patterns for topics to subscribe to |
| `exclude` | Glob patterns for topics to ignore |

### [`topic_overrides`](https://docs.rewire.run/configuration/json5-config#topic_overrides)

Per-topic overrides. Each entry has:

| Field | Description |
| --- | --- |
| `name` | Topic name. Exact match for `enabled`; may be a glob (`*`, `**`) for `max_hz` |
| `enabled` | Set to `false` to disable this topic |
| `max_hz` | Cap the topic's bridged rate in messages per second; excess messages are dropped before deserialization. Ignored on latched topics |

See [Topic Filtering](https://docs.rewire.run/configuration/topic-filtering#throttling-high-rate-topics) for matching rules and the equivalent `--throttle` flag.

### [`ws`](https://docs.rewire.run/configuration/json5-config#ws)

WebSocket server settings. Requires `--ws` flag to enable.

| Field | Description |
| --- | --- |
| `port` | WebSocket server port. Default: `9090` |
| `auth_token` | Bearer token for authentication. Required when using `--secure` |

### [`serve`](https://docs.rewire.run/configuration/json5-config#serve)

Settings for the standalone [relay](https://docs.rewire.run/relay) started with `rewire serve`. CLI flags take precedence; `cors_allowed_origins` on the command line replaces this list rather than merging. The `memory_limit` field also applies to the in-process relay started by a plain `rewire record` or `rewire play` run when no address is given.

| Field | Description |
| --- | --- |
| `bind` | Address to bind the gRPC listener on. Default: `0.0.0.0` |
| `port` | gRPC listen port. Default: `9876` |
| `memory_limit` | Buffered history limit before the oldest data is dropped (`64MiB`, `500MB`, `50%`, `unlimited`). The default keeps essentially no temporal history — see [Buffered history](https://docs.rewire.run/relay#buffered-history). Default: `1MiB` |
| `cors_allowed_origins` | Origin patterns allowed for cross-origin gRPC-web requests (browser viewers) |
| `save` | Filename stem for the relay archive, written to `<stem>_<timestamp>.rrd` |

### [`zenoh`](https://docs.rewire.run/configuration/json5-config#zenoh)

Zenoh transport settings. Ignored unless [`transport`](https://docs.rewire.run/configuration/json5-config#transport) runs Zenoh.

| Field | Description |
| --- | --- |
| `connect` | List of Zenoh router endpoints to connect to. Unset, Rewire dials `tcp/localhost:7447`, where the `rmw_zenoh` router listens |
| `multicast_scouting` | Also scout for peers over multicast, on top of dialing the endpoints above. Default: `false`, as the `rmw_zenoh` router runs with scouting off, so leaving this on will not find one |

[Bag Playback\\ \\ Stream a recorded MCAP bag to a viewer, or convert it to an RRD file.](https://docs.rewire.run/playback) [Topic Filtering\\ \\ Filter topics with glob patterns for include and exclude.](https://docs.rewire.run/configuration/topic-filtering)

### On this page

[Full example](https://docs.rewire.run/configuration/json5-config#full-example) [Fields](https://docs.rewire.run/configuration/json5-config#fields) [`app_id`](https://docs.rewire.run/configuration/json5-config#app_id) [`transport`](https://docs.rewire.run/configuration/json5-config#transport) [`domain_id`](https://docs.rewire.run/configuration/json5-config#domain_id) [`discovery_timeout`](https://docs.rewire.run/configuration/json5-config#discovery_timeout) [`queue_depth`](https://docs.rewire.run/configuration/json5-config#queue_depth) [`latched_history_depth`](https://docs.rewire.run/configuration/json5-config#latched_history_depth) [`log_level`](https://docs.rewire.run/configuration/json5-config#log_level) [`diagnostics`](https://docs.rewire.run/configuration/json5-config#diagnostics) [`output`](https://docs.rewire.run/configuration/json5-config#output) [`topics`](https://docs.rewire.run/configuration/json5-config#topics) [`topic_overrides`](https://docs.rewire.run/configuration/json5-config#topic_overrides) [`ws`](https://docs.rewire.run/configuration/json5-config#ws) [`serve`](https://docs.rewire.run/configuration/json5-config#serve) [`zenoh`](https://docs.rewire.run/configuration/json5-config#zenoh)

Last updated on August 26, 2026

© 2026 Rewire