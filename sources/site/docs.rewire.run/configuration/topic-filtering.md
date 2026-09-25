# Source: https://docs.rewire.run/configuration/topic-filtering

Configuration

# Topic Filtering

Filter topics with glob patterns for include and exclude.

Rewire supports glob patterns for selecting which topics to subscribe to. Patterns work in both CLI flags and the JSON5 config file.

## [Include patterns](https://docs.rewire.run/configuration/topic-filtering#include-patterns)

Subscribe only to topics matching a pattern:

```
rewire record --topics "/camera/*" "/odom"
```

In config:

```
{
    topics: {
        include: ["/camera/*", "/odom"],
    },
}
```

## [Exclude patterns](https://docs.rewire.run/configuration/topic-filtering#exclude-patterns)

Subscribe to everything except matching topics:

```
rewire record --exclude "/rosout" "/parameter_events" "/diagnostics/*"
```

In config:

```
{
    topics: {
        exclude: ["/rosout", "/parameter_events", "/diagnostics/*"],
    },
}
```

## [Glob syntax](https://docs.rewire.run/configuration/topic-filtering#glob-syntax)

| Pattern | Matches |
| --- | --- |
| `*` | Any sequence of characters within a path segment |
| `/camera/*` | `/camera/image`, `/camera/depth`, but not `/camera/depth/points` |
| `/robot/*/status` | `/robot/arm/status`, `/robot/base/status` |

## [Per-topic overrides](https://docs.rewire.run/configuration/topic-filtering#per-topic-overrides)

Disable specific topics by name using `topic_overrides`:

```
{
    topic_overrides: [
        { name: "/diagnostics", enabled: false },
        { name: "/camera/debug", enabled: false },
    ],
}
```

## [Throttling high-rate topics](https://docs.rewire.run/configuration/topic-filtering#throttling-high-rate-topics)

A topic that publishes faster than you need to see it can be capped, so excess messages are dropped before they are deserialized, converted, or logged:

```
rewire record --throttle "/camera/image_raw=10" "/tf=100"
```

The same caps live in the config file as `max_hz` on a `topic_overrides` entry:

```
{
    topic_overrides: [
        { name: "/camera/image_raw", max_hz: 10 },
        { name: "/lidar/**", max_hz: 5 },
    ],
}
```

Rates must be finite and positive; fractional rates like `0.5` are allowed. The [`rewire play`](https://docs.rewire.run/playback) command accepts the same `--throttle` flag, where the cap applies in bag time.

### [Matching rules](https://docs.rewire.run/configuration/topic-filtering#matching-rules)

Unlike `enabled`, a `max_hz` entry may name a glob (`*` within a segment, `**` across segments). When several entries could apply:

1. An exact topic name beats any glob.
2. Among globs, the first matching entry wins.
3. Command-line `--throttle` entries are checked before config entries, so the CLI wins per topic.

Caps are resolved when Rewire subscribes, so topics that appear later pick up matching patterns automatically.

Latched (transient-local) topics such as `/tf_static` are never throttled — a cap on one is ignored with a warning, since dropping a latched message would lose data that is only published once.

Throttling changes what Rewire bridges, not what the publisher sends. The [diagnostics](https://docs.rewire.run/diagnostics) panel keeps reporting the topic's real wire rate, and shows the effective cap beside it.

## [Precedence](https://docs.rewire.run/configuration/topic-filtering#precedence)

1. `topic_overrides` with `enabled: false` always wins
2. `--exclude` / `topics.exclude` patterns are checked next
3. `--topics` / `topics.include` patterns determine what to subscribe to
4. With neither naming a topic, everything not excluded is subscribed to — what `--all` asks for explicitly, and what a bare `rewire record` does on its own

## [Buffer side-channels](https://docs.rewire.run/configuration/topic-filtering#buffer-side-channels)

On ROS 2 Lyrical, some transports expose internal buffer side-channel topics named `<topic>/_buf_*`. These mirror a real subscription but never deliver data, so Rewire skips them during auto-subscribe. To bridge one anyway, name it explicitly with `--topics` — an exact match overrides the exclusion.

[JSON5 Config\\ \\ Configure Rewire with a JSON5 configuration file.](https://docs.rewire.run/configuration/json5-config) [Overview\\ \\ Map any ROS 2 message type to Rerun archetypes via JSON5 config.](https://docs.rewire.run/custom-mappings/overview)

### On this page

[Include patterns](https://docs.rewire.run/configuration/topic-filtering#include-patterns) [Exclude patterns](https://docs.rewire.run/configuration/topic-filtering#exclude-patterns) [Glob syntax](https://docs.rewire.run/configuration/topic-filtering#glob-syntax) [Per-topic overrides](https://docs.rewire.run/configuration/topic-filtering#per-topic-overrides) [Throttling high-rate topics](https://docs.rewire.run/configuration/topic-filtering#throttling-high-rate-topics) [Matching rules](https://docs.rewire.run/configuration/topic-filtering#matching-rules) [Precedence](https://docs.rewire.run/configuration/topic-filtering#precedence) [Buffer side-channels](https://docs.rewire.run/configuration/topic-filtering#buffer-side-channels)

Last updated on August 26, 2026

© 2026 Rewire