# Source: https://docs.rewire.run/concepts/discovery

# Topic Discovery

How Rewire finds and subscribes to ROS 2 topics.

Rewire discovers topics automatically when you run `rewire record`. No manual topic list is required (though you can provide one).

## [How discovery works](https://docs.rewire.run/concepts/discovery#how-discovery-works)

When Rewire starts, it performs discovery on both DDS and Zenoh transports simultaneously:

1. **Listen** — Rewire listens for topic announcements on the network
2. **Match** — Announced topics are matched against your include/exclude filters
3. **Subscribe** — Rewire subscribes to matching topics as they are announced and begins streaming data

Recording starts immediately: Rewire does not wait for the graph to settle before subscribing, so messages published early in a run are not missed.

## [Live discovery](https://docs.rewire.run/concepts/discovery#live-discovery)

Discovery doesn't stop after startup. Rewire continuously monitors for new topics appearing on the network:

- New topics are discovered and subscribed to automatically (if they match your filters)
- Topics that go away are detected and unsubscribed from
- No restart needed when nodes come and go

This is especially useful in development, where you frequently start and stop nodes.

## [Discovery timeout](https://docs.rewire.run/concepts/discovery#discovery-timeout)

Eager subscription is the default, and there is usually no reason to change it. If you would rather have Rewire block until the network has announced its topics — for a topic listing that is complete on the first print, say — set an explicit timeout:

```
rewire record --discovery-timeout 10
```

Or in the config file:

```
{
    discovery_timeout: 10,
}
```

Either way, topics that appear later are still picked up by live discovery.

## [Type resolution](https://docs.rewire.run/concepts/discovery#type-resolution)

For each discovered topic, Rewire needs to know the message type to deserialize it. Standard ROS 2 message schemas (the 74 built-in mappings) are bundled with Rewire, so they resolve without any workspace sourcing or `AMENT_PREFIX_PATH` configuration.

For **custom** message types, Rewire uses one of two resolution strategies depending on your environment.

### [Strategy 1: Workspace sourcing (`.msg` files on disk)](https://docs.rewire.run/concepts/discovery#strategy-1-workspace-sourcing-msg-files-on-disk)

When `AMENT_PREFIX_PATH` is set (i.e. you've sourced a ROS 2 workspace), Rewire loads type definitions from `.msg` files on disk at startup. It reads them from the `$AMENT_PREFIX_PATH/share/{pkg}/msg/{Type}.msg` path.

This is the **only** strategy available for pre-Jazzy distributions (Humble, Iron).

```
source /opt/ros/humble/setup.bash
source ~/my_ws/install/setup.bash
rewire record
```

You must source **all** workspaces that contain message definitions you need — both the base ROS 2 install and any overlay workspaces with custom messages. If a custom message package lives in a workspace you didn't source, `AMENT_PREFIX_PATH` won't include it and those types will silently fail to deserialize.

When `AMENT_PREFIX_PATH` is set, Rewire relies **entirely** on the `.msg` files it finds on disk. It does not fall back to service introspection, even if some types are missing. Make sure every message package is covered.

### [Strategy 2: Service introspection (Jazzy+)](https://docs.rewire.run/concepts/discovery#strategy-2-service-introspection-jazzy)

When `AMENT_PREFIX_PATH` is **not** set, Rewire attempts to resolve custom types via the service named `get_type_description`, exposed by Jazzy and later nodes. This happens automatically — no workspace sourcing needed.

Rewire waits up to 10 seconds for type resolution to complete. If a node doesn't expose the service (e.g. it's a pre-Jazzy node), the type can't be resolved through this path.

### [Which strategy does Rewire use?](https://docs.rewire.run/concepts/discovery#which-strategy-does-rewire-use)

| `AMENT_PREFIX_PATH` set? | Node distribution | Resolution method |
| --- | --- | --- |
| Yes | Any | `.msg` files on disk only |
| No | Jazzy+ | `get_type_description` service |
| No | Pre-Jazzy | Not possible — type is skipped |

The two strategies are **mutually exclusive**. When `AMENT_PREFIX_PATH` is defined, Rewire uses disk-based resolution and does not attempt service introspection.

### [When type resolution fails](https://docs.rewire.run/concepts/discovery#when-type-resolution-fails)

If a topic's type can't be resolved by either strategy, Rewire logs a warning and skips that topic. Common causes:

- **Unsourced workspace** — a custom message package exists in a workspace you didn't source. Source it and restart Rewire.
- **Pre-Jazzy without `AMENT_PREFIX_PATH`** — the node doesn't support `get_type_description` and there are no `.msg` files to read. Source your workspace.
- **Missing package** — the message package isn't installed at all. Install it into your workspace.

[Entity Mapping\\ \\ How ROS 2 topics become Rerun entities in the viewer.](https://docs.rewire.run/concepts/entity-mapping) [Transport Selection\\ \\ Choosing between DDS and Zenoh for your ROS 2 network.](https://docs.rewire.run/transport-selection)

### On this page

[How discovery works](https://docs.rewire.run/concepts/discovery#how-discovery-works) [Live discovery](https://docs.rewire.run/concepts/discovery#live-discovery) [Discovery timeout](https://docs.rewire.run/concepts/discovery#discovery-timeout) [Type resolution](https://docs.rewire.run/concepts/discovery#type-resolution) [Strategy 1: Workspace sourcing (`.msg` files on disk)](https://docs.rewire.run/concepts/discovery#strategy-1-workspace-sourcing-msg-files-on-disk) [Strategy 2: Service introspection (Jazzy+)](https://docs.rewire.run/concepts/discovery#strategy-2-service-introspection-jazzy) [Which strategy does Rewire use?](https://docs.rewire.run/concepts/discovery#which-strategy-does-rewire-use) [When type resolution fails](https://docs.rewire.run/concepts/discovery#when-type-resolution-fails)

Last updated on August 26, 2026

© 2026 Rewire