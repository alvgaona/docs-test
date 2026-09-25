# Source: https://docs.rewire.run/playback

# Bag Playback

Stream a recorded MCAP bag to a viewer, or convert it to an RRD file.

The `rewire play` command reads a recorded MCAP bag and runs it through the same converters as the live bridge. Bags carry their own message definitions, so playback needs no ROS 2 installation, no sourced workspace, and no DDS traffic on the network.

Two modes, never combined: without `--save` the bag streams live to a viewer or [relay](https://docs.rewire.run/relay), paced by its log times; with `--save` it converts to an RRD file as fast as the machine allows.

## [Stream a bag to the viewer](https://docs.rewire.run/playback#stream-a-bag-to-the-viewer)

```
rewire play flight.mcap
```

This starts the bundled viewer the same way `rewire record` does, then replays the bag at the pace it was recorded at. Use `--rate` to change that pace:

```
rewire play flight.mcap --rate 4      # four times faster
rewire play flight.mcap --rate 0.25   # quarter speed
```

To stream to a viewer or relay that is already running, point at it with `--connect`:

```
rewire play flight.mcap --connect 192.168.1.50:9876
```

## [Convert a bag to RRD](https://docs.rewire.run/playback#convert-a-bag-to-rrd)

```
rewire play flight.mcap -o flight.rrd
```

Conversion ignores log-time pacing and runs as fast as the CPU allows, writing exactly the path you name. The result opens in the Rerun viewer like any other `.rrd` file. Because there is no viewer to feed, `--save` and `--connect` are mutually exclusive.

The `--output` flag works as an alias for `--save`, matching the vocabulary of `rerun mcap convert`.

## [Selecting and shaping the data](https://docs.rewire.run/playback#selecting-and-shaping-the-data)

Topic selection, exclusion, throttling, and custom mappings behave exactly as they do on a live `rewire record` run:

```
rewire play flight.mcap \
    --topics "/camera/*" "/odom" \
    --exclude "/rosout" \
    --throttle "/tf=100"
```

An empty selection plays every topic in the bag. Throttles are applied in bag time, so the cap above admits at most 100 messages per second of recorded time regardless of the playback rate. See [Topic Filtering](https://docs.rewire.run/configuration/topic-filtering) for pattern syntax and throttling rules.

## [Flags](https://docs.rewire.run/playback#flags)

| Flag | Short | Description | Default |
| --- | --- | --- | --- |
| `<INPUT>` | — | Path to the MCAP bag file | — |
| `--save <PATH>` | `-o` | Convert to this RRD file instead of streaming | — |
| `--connect <ADDRESS>` | — | Stream to a running relay or Rerun viewer | — |
| `--rate <RATE>` | — | Playback rate multiplier (streaming mode only) | `1.0` |
| `--flush-tick-ms <MS>` | — | How often the live stream is flushed to the viewer, in milliseconds (streaming mode only) | `8` |
| `--scalar-flush-tick-ms <MS>` | — | Hold plot-class chunks this long and forward them merged into one chunk per entity; `0` disables (streaming mode only) | `150` |
| `--live-queue-mib <MIB>` | — | Byte budget for the live stream's never-blocking queue; `0` disables the bound (streaming mode only) | `64` |
| `--topics <TOPICS>` | `-t` | Play only these topics | _(all topics)_ |
| `--exclude <EXCLUDE>` | `-e` | Exclude topics by glob pattern | — |
| `--throttle <TOPIC=HZ>` | — | Cap a topic's rate in bag time (repeatable) | — |
| `--mappings <PATH>` | — | Load custom message mappings | — |
| `--config <PATH>` | — | Load JSON5 config file | `~/.config/rewire/config.json5` |

## [Message types](https://docs.rewire.run/playback#message-types)

Playback uses the message definitions embedded in the bag, so custom types resolve without sourcing a workspace. Topics whose type has no converter are skipped, exactly as they are on a live bridge — run `rewire types` to see what is mapped, or write a [custom mapping](https://docs.rewire.run/custom-mappings/overview) and pass it with `--mappings`.

[Runtime Control\\ \\ Control a running Rewire instance without restarting it.](https://docs.rewire.run/concepts/runtime-control) [JSON5 Config\\ \\ Configure Rewire with a JSON5 configuration file.](https://docs.rewire.run/configuration/json5-config)

### On this page

[Stream a bag to the viewer](https://docs.rewire.run/playback#stream-a-bag-to-the-viewer) [Convert a bag to RRD](https://docs.rewire.run/playback#convert-a-bag-to-rrd) [Selecting and shaping the data](https://docs.rewire.run/playback#selecting-and-shaping-the-data) [Flags](https://docs.rewire.run/playback#flags) [Message types](https://docs.rewire.run/playback#message-types)

Last updated on August 26, 2026

© 2026 Rewire