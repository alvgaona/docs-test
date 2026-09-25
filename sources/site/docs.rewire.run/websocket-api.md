# Source: https://docs.rewire.run/websocket-api

# WebSocket API

Stream live ROS 2 topic data to any client over WebSocket.

Rewire can expose live ROS 2 topic data over WebSocket, so any language or tool can consume it without touching DDS, Zenoh, or Rust. The WebSocket server runs alongside the Rerun output and streams all deserializable topics — not just those with registered Rerun converters.

## [Enable the WebSocket server](https://docs.rewire.run/websocket-api#enable-the-websocket-server)

```
rewire record --ws
```

The server listens on port `9090` by default. Use `--ws-port` to change it:

```
rewire record --ws --ws-port 8080
```

Or configure it in your JSON5 config file:

```
{
    ws: {
        port: 9090,
    },
}
```

## [Protocol](https://docs.rewire.run/websocket-api#protocol)

The WebSocket API uses JSON text frames for commands and responses.

### [`list_topics`](https://docs.rewire.run/websocket-api#list_topics)

Returns all discovered topics with their type names.

```
{ "command": "list_topics" }
```

A REST endpoint is also available for non-WebSocket clients:

```
GET http://localhost:9090/topics
```

### [`subscribe`](https://docs.rewire.run/websocket-api#subscribe)

Subscribe to one or more topics. The server sends the schema first, then streams JSON data frames.

```
{ "command": "subscribe", "topics": ["/camera/image", "/odom"] }
```

### [`unsubscribe`](https://docs.rewire.run/websocket-api#unsubscribe)

Stop receiving data for specified topics.

```
{ "command": "unsubscribe", "topics": ["/camera/image"] }
```

### [`list_nodes`](https://docs.rewire.run/websocket-api#list_nodes)

Returns full node information including publishers, subscribers, and services.

```
{ "command": "list_nodes" }
```

### [`watch_graph`](https://docs.rewire.run/websocket-api#watch_graph)

Subscribe to live discovery events. The server pushes notifications when nodes and topics appear or disappear.

```
{ "command": "watch_graph" }
```

Events:

- `topic_added` — a new topic was discovered
- `topic_removed` — a topic went away
- `node_added` — a new node joined the network
- `node_removed` — a node left the network

## [Wire formats](https://docs.rewire.run/websocket-api#wire-formats)

By default, data is serialized as JSON text frames. Clients can negotiate alternative formats per-subscription for better performance.

### [CDR (raw binary)](https://docs.rewire.run/websocket-api#cdr-raw-binary)

Request raw CDR bytes as binary WebSocket frames, skipping JSON serialization entirely:

```
{ "command": "subscribe", "topics": ["/pointcloud"], "format": "cdr" }
```

### [ZSTD compression](https://docs.rewire.run/websocket-api#zstd-compression)

Enable ZSTD compression on binary frames:

```
{ "command": "subscribe", "topics": ["/pointcloud"], "compression": "zstd" }
```

CDR and ZSTD can be combined:

```
{ "command": "subscribe", "topics": ["/pointcloud"], "format": "cdr", "compression": "zstd" }
```

## [Authentication](https://docs.rewire.run/websocket-api#authentication)

Enable bearer token authentication with the `--secure` flag. The token must be configured in your JSON5 config file before starting Rewire — the server will not start without it:

```
{
    ws: {
        auth_token: "your-secret-token",
    },
}
```

```
rewire record --ws --secure
```

Authentication is validated at the HTTP upgrade level — unauthenticated clients never get a WebSocket connection.

## [Rate limiting](https://docs.rewire.run/websocket-api#rate-limiting)

Clients can request server-side rate limiting per topic using the `set_rate` command:

```
{ "command": "set_rate", "topic": "/camera/image", "max_hz": 10 }
```

The server drops messages exceeding the specified rate. This is useful for high-frequency topics where the client only needs a subset of the data.

[Diagnostics\\ \\ Per-topic Hz, bandwidth, drops, and latency monitoring.](https://docs.rewire.run/diagnostics) [CLI Reference\\ \\ Complete command reference for the Rewire CLI.](https://docs.rewire.run/cli-reference)

### On this page

[Enable the WebSocket server](https://docs.rewire.run/websocket-api#enable-the-websocket-server) [Protocol](https://docs.rewire.run/websocket-api#protocol) [`list_topics`](https://docs.rewire.run/websocket-api#list_topics) [`subscribe`](https://docs.rewire.run/websocket-api#subscribe) [`unsubscribe`](https://docs.rewire.run/websocket-api#unsubscribe) [`list_nodes`](https://docs.rewire.run/websocket-api#list_nodes) [`watch_graph`](https://docs.rewire.run/websocket-api#watch_graph) [Wire formats](https://docs.rewire.run/websocket-api#wire-formats) [CDR (raw binary)](https://docs.rewire.run/websocket-api#cdr-raw-binary) [ZSTD compression](https://docs.rewire.run/websocket-api#zstd-compression) [Authentication](https://docs.rewire.run/websocket-api#authentication) [Rate limiting](https://docs.rewire.run/websocket-api#rate-limiting)

Last updated on August 26, 2026

© 2026 Rewire