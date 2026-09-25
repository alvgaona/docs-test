# Source: https://docs.rewire.run/cli-reference

# CLI Reference

Complete command reference for the Rewire CLI.

## [Global flags](https://docs.rewire.run/cli-reference#global-flags)

Accepted before any subcommand.

| Flag | Short | Description | Default |
| --- | --- | --- | --- |
| `--version` | `-v`, `-V` | Print the version and exit | — |
| `--help` | `-h` | Print help | — |
| `--color <WHEN>` | — | When to colorize output (`auto`, `always`, `never`) | `auto` |

Verbosity is per-subcommand rather than global. [`record`](https://docs.rewire.run/cli-reference#rewire-record), [`play`](https://docs.rewire.run/cli-reference#rewire-play) and [`serve`](https://docs.rewire.run/cli-reference#rewire-serve) take `--log-level`; it outranks the `RUST_LOG` environment variable, which in turn outranks the `log_level` [config key](https://docs.rewire.run/configuration/json5-config). Every other subcommand reads the environment variable and the config key only. For the full version table, covering the viewer and dependencies as well, use [`rewire version`](https://docs.rewire.run/cli-reference#rewire-version).

## [`rewire record`](https://docs.rewire.run/cli-reference#rewire-record)

Subscribe to ROS 2 topics and stream data to Rerun.

```
rewire record [OPTIONS]
```

| Flag | Short | Description | Default |
| --- | --- | --- | --- |
| `--all` | `-a` | Subscribe to all discovered topics — already the default when nothing else selects any | _(implied)_ |
| `--topics <TOPICS>` | `-t` | Subscribe to specific topics | — |
| `--exclude <EXCLUDE>` | `-e` | Exclude topics by glob pattern | — |
| `--connect <ADDRESS>` | — | Stream to remote viewer or [relay](https://docs.rewire.run/relay) ([shorthand accepted](https://docs.rewire.run/viewer#using-a-remote-viewer)) | — |
| `--save [STEM]` | — | Save each recording to `<stem>_<recording_id>.rrd`, _and_ stream live; bare flag uses `<app_id>_<timestamp>` as the stem | — |
| `--save-session` | — | With `--save`, append every recording of the run to one `<stem>_<timestamp>.rrd` | `false` |
| `--no-live` | — | Save with no live half: no port probe, no connect, no viewer spawn. Requires `--save`; conflicts with `--connect` | `false` |
| `--flush-tick-ms <MS>` | — | How often the live stream is flushed to the viewer, in milliseconds; ignored with `--no-live` | `8` |
| `--scalar-flush-tick-ms <MS>` | — | Hold plot-class chunks (scalars, series styling, text logs) this long and forward them merged into one chunk per entity; `0` disables | `150` |
| `--live-queue-mib <MIB>` | — | Byte budget for the live stream's never-blocking queue; a slow consumer costs counted drops of the oldest data instead of stalling the bridge. `0` disables the bound | `64` |
| `--config <PATH>` | — | Load JSON5 config file | `~/.config/rewire/config.json5` |
| `--mappings <PATH>` | — | Load custom message mappings | — |
| `--transport <all|dds|zenoh>` | — | Wire transports to start ([details](https://docs.rewire.run/transport-selection#choosing-transports)) | `all` |
| `--domain-id <ID>` | — | ROS 2 domain ID | `$ROS_DOMAIN_ID` or `0` |
| `--diagnostics` | — | Enable per-topic diagnostics | `false` |
| `--ws` | — | Enable WebSocket server | `false` |
| `--ws-port <PORT>` | — | WebSocket server port | `9090` |
| `--secure` | — | Require bearer token authentication for WebSocket | `false` |
| `--zenoh-connect <ENDPOINTS>` | — | Zenoh router endpoints (e.g. `tcp/192.168.1.100:7447`) | — |
| `--throttle <TOPIC=HZ>` | — | Cap a topic's bridged rate ([details](https://docs.rewire.run/configuration/topic-filtering#throttling-high-rate-topics), repeatable) | — |
| `--discovery-timeout <SECS>` | — | Seconds to block on discovery before subscribing; unset, subscription is eager | _(unset)_ |
| `--queue-depth <N>` | — | Message queue depth | — |
| `--latched-depth <N>` | — | History depth for latched (transient-local) topics like `/tf_static` | — |
| `--log-level <FILTER>` | — | Log filter directives, e.g. `bridge=debug` or `info,iris=trace` | `$RUST_LOG` or the `log_level` config key |

Running `rewire record` with no topic flags subscribes to everything on the graph. Pass `--all` explicitly only to override an `include` list in the config file; `--exclude` narrows either.

The `--save` flag takes a filename _stem_, not a full path with extension — a `.rrd` suffix on the stem is ignored and Rewire completes the name so that concurrent or successive recordings never overwrite each other. A `rewire log` swap rotates to a fresh file automatically.

Saving also streams: a `--save` run resolves the same live half as a flagless one, connecting to whatever already holds port `9876` or starting the embedded [relay](https://docs.rewire.run/relay) and viewer, so you can watch what you are capturing. Add `--no-live` for file-only output on measurement runs and machines with no display.

## [`rewire play`](https://docs.rewire.run/cli-reference#rewire-play)

Stream a recorded MCAP bag to a viewer, or convert it to an RRD file. See [Bag Playback](https://docs.rewire.run/playback) for the full guide.

```
rewire play <INPUT> [OPTIONS]
```

| Flag | Short | Description | Default |
| --- | --- | --- | --- |
| `--save <PATH>` | `-o` | Convert the bag to this RRD file instead of streaming (unpaced) | — |
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
| `--log-level <FILTER>` | — | Log filter directives, e.g. `bridge=debug` or `info,iris=trace` | `$RUST_LOG` or the `log_level` config key |

The `--save` and `--connect` flags are mutually exclusive: conversion writes exactly the path you name and never connects to a viewer.

## [`rewire serve`](https://docs.rewire.run/cli-reference#rewire-serve)

Run a standalone headless [relay](https://docs.rewire.run/relay) that bridges stream to and viewers connect through.

```
rewire serve [OPTIONS]
```

| Flag | Description | Default |
| --- | --- | --- |
| `--bind <ADDR>` | Address to bind the gRPC listener on | `0.0.0.0` |
| `--port <PORT>` | gRPC listen port | `9876` |
| `--memory-limit <LIMIT>` | Buffered history limit before the oldest data is dropped (`64MiB`, `500MB`, `50%`, `unlimited`). The default keeps essentially no temporal history — see [Buffered history](https://docs.rewire.run/relay#buffered-history) | `1MiB` |
| `--cors-allowed-origin <PATTERN>` | Additional origin allowed for cross-origin gRPC-web requests (repeatable) | — |
| `--save [STEM]` | Archive relay traffic to `<stem>_<timestamp>.rrd`; bare flag uses `rewire-relay` as the stem | — |
| `--config <PATH>` | Load JSON5 config file (`serve` section) | `~/.config/rewire/config.json5` |
| `--log-level <FILTER>` | Log filter directives, e.g. `bridge=debug` or `info,iris=trace` | `$RUST_LOG` or the `log_level` config key |

## [`rewire status`](https://docs.rewire.run/cli-reference#rewire-status)

Inspect a running [relay](https://docs.rewire.run/relay) — its uptime, buffer memory, and connected bridges with their state, app, recording ID, and last-heard time.

```
rewire status [OPTIONS]
```

| Flag | Description | Default |
| --- | --- | --- |
| `--connect <ADDRESS>` | Relay to query (`host`, `host:port`, or full URL) | `127.0.0.1:9876` |

## [`rewire log`](https://docs.rewire.run/cli-reference#rewire-log)

Start a new recording on a running bridge instance via the loopback gRPC control plane.

```
rewire log [OPTIONS]
```

| Flag | Description | Default |
| --- | --- | --- |
| `--app <APP>` | Application ID for the recording | `"rewire"` |
| `--id <ID>` | Custom recording ID | Auto-generated |
| `--pid <PID>` | Target a specific bridge instance | — |

## [`rewire types`](https://docs.rewire.run/cli-reference#rewire-types)

List all supported ROS 2 message types and their corresponding Rerun archetypes.

```
rewire types [OPTIONS]
```

| Flag | Description | Default |
| --- | --- | --- |
| `--json` | Print the mappings as JSON instead of a table | _(table)_ |

The JSON form prints an array of `{ "ros_type", "archetypes" }` objects sorted by type name, holding the same mappings the table shows. It is uncolored and stable enough to pipe into `jq`.

## [`rewire doctor`](https://docs.rewire.run/cli-reference#rewire-doctor)

Run environment health checks.

```
rewire doctor [OPTIONS]
```

| Flag | Description | Default |
| --- | --- | --- |
| `--transport <all|dds|zenoh>` | Wire transports to start ([details](https://docs.rewire.run/transport-selection#choosing-transports)) | `all` |
| `--domain-id <ID>` | ROS 2 domain ID | `$ROS_DOMAIN_ID` or `0` |
| `--zenoh-connect <ENDPOINTS>` | Zenoh router endpoints | — |
| `--discovery-timeout <SECS>` | Seconds to wait for discovery before reporting | `2` |
| `--config <PATH>` | Load JSON5 config file | `~/.config/rewire/config.json5` |

Checks:

- DDS middleware availability
- Zenoh connectivity
- Network configuration
- Converter readiness
- System limits, including a warning when Linux `net.core.rmem_max` is below the receive buffer Rewire asks for (the default 208 KB silently drops fragmented images and point clouds under load)

## [`rewire config`](https://docs.rewire.run/cli-reference#rewire-config)

Manage the Rewire configuration file.

### [`rewire config path`](https://docs.rewire.run/cli-reference#rewire-config-path)

Print the active config file path.

```
rewire config path
```

### [`rewire config generate`](https://docs.rewire.run/cli-reference#rewire-config-generate)

Generate a starter JSON5 config file with all available fields documented.

```
rewire config generate [OPTIONS]
```

| Flag | Short | Description | Default |
| --- | --- | --- | --- |
| `--output <PATH>` | `-o` | Write output to a file instead of stdout | _(prints to stdout)_ |

## [`rewire upgrade`](https://docs.rewire.run/cli-reference#rewire-upgrade)

Update Rewire to the latest version.

```
rewire upgrade [OPTIONS]
```

| Flag | Description | Default |
| --- | --- | --- |
| `--relocate` | Move the install into `~/.local/bin` so upgrades stop needing `sudo` | _(upgrades in place)_ |

Both binaries are upgraded. The bridge installs the `rewire-viewer` bundled with the release it fetches, so the pair stays matched, and a viewer that has been released on its own since is brought up to date too.

Upgrading replaces a binary by renaming over it, which needs write permission on the install directory rather than on the file itself. An install in a directory you own — the install script's `~/.local/bin` default — needs no elevated rights. A root-owned install such as `/usr/local/bin` does, and the upgrade says so before it downloads anything.

Passing `--relocate` copies both binaries into `~/.local/bin`, updates them, and clears the originals once the new location is reachable on `PATH`. Run it without `sudo` — under `sudo`, `HOME` and `PATH` belong to root, so the check guarding the deletion would answer for the wrong environment.

The `rewire upgrade` command only works for installs via the install script. If you installed via pixi or apt, use your package manager to update (`pixi update` or `apt upgrade`).

## [`rewire uninstall`](https://docs.rewire.run/cli-reference#rewire-uninstall)

Remove Rewire and the bundled viewer from the directory they were installed into.

```
rewire uninstall [OPTIONS]
```

| Flag | Short | Description | Default |
| --- | --- | --- | --- |
| `--purge` | — | Remove the config file and logs as well | _(both kept)_ |
| `--yes` | `-y` | Skip the confirmation prompt | `false` |

Both binaries go, along with the version cache kept beside them. The config file and the logs are left where they are, so a reinstall lands on the settings you already had. Pass `--purge` to remove those too.

The command prints everything it is about to delete and waits for confirmation. If the install directory is not writable it says so before the prompt, while you can still answer no, rather than failing partway through. With no terminal to ask on it stops instead of assuming an answer, so unattended runs need `--yes`.

Only standalone installs are removed. If a package manager owns the install, the command refuses and names the one to run instead — `sudo apt remove rewire`, `pixi global uninstall rewire`, or `brew uninstall rewire`. A binary running from a `cargo` build is refused as a build artifact, with `cargo clean` as the answer.

## [`rewire mappings generate`](https://docs.rewire.run/cli-reference#rewire-mappings-generate)

Inspect discovered topics and generate a JSON5 custom mappings template. Spawns a type resolver automatically and waits for custom schemas to resolve before writing the output file.

```
rewire mappings generate <TOPICS>... [OPTIONS]
```

| Flag | Short | Description | Default |
| --- | --- | --- | --- |
| `<TOPICS>...` | — | Topic names to generate templates for (required) | — |
| `--output <PATH>` | `-o` | Write output to a file instead of stdout | _(prints to stdout)_ |
| `--transport <all|dds|zenoh>` | — | Wire transports to start ([details](https://docs.rewire.run/transport-selection#choosing-transports)) | `all` |
| `--domain-id <ID>` | — | ROS 2 domain ID | `$ROS_DOMAIN_ID` or `0` |
| `--zenoh-connect <ENDPOINTS>` | — | Zenoh router endpoints | — |
| `--discovery-timeout <SECS>` | — | Seconds to wait for discovery | `5` |
| `--resolve-timeout <SECS>` | — | Seconds to wait for schema resolution via `get_type_description` (Jazzy+) | `15` |

Discovery settings are read from the config file at its default location, so a `transport` or `domain_id` set there applies here as it does to `rewire record`.

## [`rewire version`](https://docs.rewire.run/cli-reference#rewire-version)

Show Rewire, Rewire Viewer, and dependency versions.

```
rewire version
```

## [`rewire completions`](https://docs.rewire.run/cli-reference#rewire-completions)

Generate shell completions.

```
rewire completions <SHELL>
```

| Shell | Output location |
| --- | --- |
| `bash` | `~/.bash_completion.d/rewire` |
| `zsh` | `~/.zfunc/_rewire` |
| `fish` | `~/.config/fish/completions/rewire.fish` |
| `elvish` | — |
| `powershell` | — |

[WebSocket API\\ \\ Stream live ROS 2 topic data to any client over WebSocket.](https://docs.rewire.run/websocket-api) [Roadmap\\ \\ Planned features and improvements for Rewire.](https://docs.rewire.run/roadmap)

### On this page

[Global flags](https://docs.rewire.run/cli-reference#global-flags) [`rewire record`](https://docs.rewire.run/cli-reference#rewire-record) [`rewire play`](https://docs.rewire.run/cli-reference#rewire-play) [`rewire serve`](https://docs.rewire.run/cli-reference#rewire-serve) [`rewire status`](https://docs.rewire.run/cli-reference#rewire-status) [`rewire log`](https://docs.rewire.run/cli-reference#rewire-log) [`rewire types`](https://docs.rewire.run/cli-reference#rewire-types) [`rewire doctor`](https://docs.rewire.run/cli-reference#rewire-doctor) [`rewire config`](https://docs.rewire.run/cli-reference#rewire-config) [`rewire config path`](https://docs.rewire.run/cli-reference#rewire-config-path) [`rewire config generate`](https://docs.rewire.run/cli-reference#rewire-config-generate) [`rewire upgrade`](https://docs.rewire.run/cli-reference#rewire-upgrade) [`rewire uninstall`](https://docs.rewire.run/cli-reference#rewire-uninstall) [`rewire mappings generate`](https://docs.rewire.run/cli-reference#rewire-mappings-generate) [`rewire version`](https://docs.rewire.run/cli-reference#rewire-version) [`rewire completions`](https://docs.rewire.run/cli-reference#rewire-completions)

Last updated on August 26, 2026

© 2026 Rewire