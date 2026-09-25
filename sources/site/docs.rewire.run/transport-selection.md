# Source: https://docs.rewire.run/transport-selection

# Transport Selection

Choosing between DDS and Zenoh for your ROS 2 network.

Rewire supports both DDS and Zenoh simultaneously. This guide helps you understand the trade-offs and choose the right transport for your setup.

You don't have to choose one. If your network has nodes on both DDS and Zenoh, Rewire bridges both at the same time without configuration — that is the default.

## [DDS vs Zenoh](https://docs.rewire.run/transport-selection#dds-vs-zenoh)

DDSZenoh

DDS (Data Distribution Service) is the default middleware for ROS 2. Most ROS 2 installations use either CycloneDDS or FastDDS.

**When to use DDS:**

- You're running a standard ROS 2 setup with the default middleware
- All nodes are on the same local network or subnet
- You need zero configuration — DDS discovery works out of the box on a LAN
- You're using CycloneDDS or FastDDS

**Considerations:**

- DDS discovery uses multicast, which may not work across subnets or VPNs without extra configuration
- Large-scale deployments (many nodes/topics) can generate significant discovery traffic
- Some cloud and container environments block multicast by default

Zenoh is a newer protocol adopted by ROS 2 via `rmw_zenoh`. It's designed for wide-area networks and resource-constrained environments.

**When to use Zenoh:**

- Your ROS 2 nodes use `rmw_zenoh` as the middleware
- You need to bridge across subnets, VPNs, or cloud environments
- You want lower discovery overhead in large-scale deployments
- You're running a fleet of robots that need to communicate across networks

**Considerations:**

- Requires `rmw_zenoh` or `zenoh-plugin-ros2dds` on the ROS 2 side
- Less mature than DDS in the ROS 2 ecosystem

## [Mixed networks](https://docs.rewire.run/transport-selection#mixed-networks)

Rewire discovers topics on both DDS and Zenoh at the same time. If a topic is available on both transports, Rewire subscribes via whichever transport discovered it first.

## [Choosing transports](https://docs.rewire.run/transport-selection#choosing-transports)

By default Rewire starts both, which is the right answer for almost every setup: it costs a Zenoh session that finds nothing when there is no router, and it means a node that moves between middlewares needs no change here.

Narrow it with `--transport` when you know only one is in play:

CLIJSON5 config

```
rewire record --transport dds      # DDS only
rewire record --transport zenoh    # Zenoh only
rewire record --transport all      # both (the default)
```

```
{
    transport: "dds",
}
```

The flag is shared by `rewire record`, `rewire doctor` and `rewire mappings generate`, and a flag always beats the config key.

The case for narrowing is a DDS-only deployment: starting Zenoh there carries a session and a background dial loop against a router that was never meant to exist. Picking `zenoh` likewise skips DDS discovery traffic on a fleet that has moved to `rmw_zenoh`. The `zenoh` section of the config is ignored unless the choice actually runs Zenoh.

## [Configuration](https://docs.rewire.run/transport-selection#configuration)

### [DDS domain ID](https://docs.rewire.run/transport-selection#dds-domain-id)

```
rewire record --domain-id 42
```

Or set the `ROS_DOMAIN_ID` environment variable. Default is `0`.

### [Zenoh router](https://docs.rewire.run/transport-selection#zenoh-router)

Unset, Rewire dials `tcp/localhost:7447`, where the `rmw_zenoh` router listens. If your Zenoh nodes are behind a router elsewhere, pass the endpoint via CLI or config:

CLIJSON5 config

```
rewire record --zenoh-connect tcp/192.168.1.100:7447
```

```
{
    zenoh: {
        connect: ["tcp/192.168.1.100:7447"],
    },
}
```

Set `zenoh.multicast_scouting: true` to also scout for peers over multicast, on top of dialing the endpoints above. It is off by default because the `rmw_zenoh` router runs with scouting off too, so turning it on will not find one.

[Topic Discovery\\ \\ How Rewire finds and subscribes to ROS 2 topics.](https://docs.rewire.run/concepts/discovery) [Runtime Control\\ \\ Control a running Rewire instance without restarting it.](https://docs.rewire.run/concepts/runtime-control)

### On this page

[DDS vs Zenoh](https://docs.rewire.run/transport-selection#dds-vs-zenoh) [Mixed networks](https://docs.rewire.run/transport-selection#mixed-networks) [Choosing transports](https://docs.rewire.run/transport-selection#choosing-transports) [Configuration](https://docs.rewire.run/transport-selection#configuration) [DDS domain ID](https://docs.rewire.run/transport-selection#dds-domain-id) [Zenoh router](https://docs.rewire.run/transport-selection#zenoh-router)

Last updated on August 26, 2026

© 2026 Rewire