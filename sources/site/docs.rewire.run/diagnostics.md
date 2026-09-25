# Source: https://docs.rewire.run/diagnostics

# Diagnostics

Per-topic Hz, bandwidth, drops, and latency monitoring.

Rewire can log per-topic diagnostics as Rerun `Scalars` entities, giving you real-time visibility into your ROS 2 data pipeline.

## [Enable diagnostics](https://docs.rewire.run/diagnostics#enable-diagnostics)

CLIJSON5 config

```
rewire record --diagnostics
```

```
{
    diagnostics: true,
}
```

## [Metrics](https://docs.rewire.run/diagnostics#metrics)

Diagnostics are sampled every second. Each topic logs a single `Scalars` row at `/rewire/bridge/topics/{topic}` carrying four named series:

| Series | Description |
| --- | --- |
| `hz` | Messages per second |
| `bytes_per_sec` | Bandwidth in bytes per second |
| `drops` | Total dropped messages |
| `latency_ms` | Exponential moving average latency (ms) |

A topic without a latency sample — its clock delta was discarded, or no sample has arrived yet — logs the first three series and leaves `latency_ms` without a point. To isolate one series in a plot, click it in the legend to toggle it, or Alt-click to solo it.

Topics with a [rate cap](https://docs.rewire.run/configuration/topic-filtering#throttling-high-rate-topics) show the effective cap next to their measured Hz in the viewer's Diagnostics panel. The Hz figure itself is always the rate on the wire, not the throttled rate Rewire bridges.

## [Aggregate metrics](https://docs.rewire.run/diagnostics#aggregate-metrics)

In addition to per-topic metrics, Rewire logs aggregate counters:

| Metric | Entity path |
| --- | --- |
| Active topics | `/rewire/bridge/active_topics` |
| Subscribed topics | `/rewire/bridge/subscribed_topics` |
| Total bytes/sec | `/rewire/bridge/total_bytes_per_sec` |
| Total drops | `/rewire/bridge/total_drops` |
| Total Hz | `/rewire/bridge/total_hz` |

## [Viewing in Rerun](https://docs.rewire.run/diagnostics#viewing-in-rerun)

Diagnostics appear as scalar time series in the Rerun viewer. You can:

- Plot any metric over time in the timeline panel
- Compare Hz across topics to find bottlenecks
- Monitor bandwidth to detect saturation
- Track drops to identify QoS mismatches

## [What latency measures](https://docs.rewire.run/diagnostics#what-latency-measures)

Latency is the time between a message being published and Rewire receiving it, taken from the transport timestamp the publishing process stamps on the wire. It does not read `header.stamp`, so it stays meaningful for topics whose messages carry old stamps — a recording replayed with a tool like `ros2 bag play`, for instance — and does not depend on publisher clock discipline or on whether you run in sim time.

Deltas beyond 60 seconds in either direction cannot be transport latency and usually mean a host whose clock is out of sync. Those samples are discarded, and the panel shows `-` for the topic.

## [Latency smoothing](https://docs.rewire.run/diagnostics#latency-smoothing)

Latency uses exponential moving average (EMA) with a 0.1 smoothing factor:

```
latency = 0.1 * current + 0.9 * previous
```

This filters out one-off spikes while tracking trends.

[Transform Tree\\ \\ Full TF tree visualization with coordinate frames and axes.](https://docs.rewire.run/urdf-tf/transform-tree) [WebSocket API\\ \\ Stream live ROS 2 topic data to any client over WebSocket.](https://docs.rewire.run/websocket-api)

### On this page

[Enable diagnostics](https://docs.rewire.run/diagnostics#enable-diagnostics) [Metrics](https://docs.rewire.run/diagnostics#metrics) [Aggregate metrics](https://docs.rewire.run/diagnostics#aggregate-metrics) [Viewing in Rerun](https://docs.rewire.run/diagnostics#viewing-in-rerun) [What latency measures](https://docs.rewire.run/diagnostics#what-latency-measures) [Latency smoothing](https://docs.rewire.run/diagnostics#latency-smoothing)

Last updated on August 26, 2026

© 2026 Rewire