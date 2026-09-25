# Source: https://docs.rewire.run/custom-mappings/archetypes

Custom Mappings

# Archetypes

Available Rerun archetypes for custom message mappings.

## [Leaf archetypes](https://docs.rewire.run/custom-mappings/archetypes#leaf-archetypes)

Simple one-to-one field mappings:

| Archetype | Description | Field type |
| --- | --- | --- |
| `Scalars` | Numeric time series | Any numeric field |
| `TextLog` | Log entry with severity | String field |
| `TextDocument` | Rich text display | String field |

```
{
  temperature: { archetype: "Scalars" },
  message: { archetype: "TextLog" },
  description: { archetype: "TextDocument" },
}
```

## [Composite archetypes](https://docs.rewire.run/custom-mappings/archetypes#composite-archetypes)

Map multiple sub-fields to a single Rerun archetype:

| Archetype | Required fields | Optional fields |
| --- | --- | --- |
| `Points2D` | `x`, `y` | `label`, `color` |
| `Points3D` | `x`, `y`, `z` | `label`, `color` |
| `Boxes2D` | `x`, `y`, `w`, `h` | `label` |
| `Boxes3D` | `x`, `y`, `z`, `w`, `h`, `d` | `label` |
| `InstancePoses3D` | `x`, `y`, `z` | `qx`, `qy`, `qz`, `qw` |
| `Arrows3D` | `ox`, `oy`, `oz`, `dx`, `dy`, `dz` | — |
| `GeoPoints` | `lat`, `lon` | `alt` |
| `Transform3D` | `x`, `y`, `z` | `qx`, `qy`, `qz`, `qw` |
| `LineStrips3D` | `points` | — |

```
{
  position: {
    archetype: "Points3D",
    x: "x", y: "y", z: "z",
    label: "name",
  },
}
```

## [Array expansion](https://docs.rewire.run/custom-mappings/archetypes#array-expansion)

Use `each` to iterate over arrays and log each element:

```
{
  detections: {
    archetype: "Points3D",
    each: ".",
    x: "position.x", y: "position.y", z: "position.z",
    label: "class",
  },
}
```

The `each` field specifies the path to the array. Use `"."` when the field itself is the array. Each element is logged individually.

## [Nested fields](https://docs.rewire.run/custom-mappings/archetypes#nested-fields)

Recurse into sub-messages:

```
{
  position: {
    fields: {
      x: { archetype: "Scalars" },
      y: { archetype: "Scalars" },
      z: { archetype: "Scalars" },
    }
  },
}
```

## [Entity keys](https://docs.rewire.run/custom-mappings/archetypes#entity-keys)

Use dot notation to access nested fields:

```
{
  target: {
    archetype: "Points3D",
    x: "pose.position.x",
    y: "pose.position.y",
    z: "pose.position.z",
  },
}
```

[Overview\\ \\ Map any ROS 2 message type to Rerun archetypes via JSON5 config.](https://docs.rewire.run/custom-mappings/overview) [Examples\\ \\ Example custom mapping configurations.](https://docs.rewire.run/custom-mappings/examples)

### On this page

[Leaf archetypes](https://docs.rewire.run/custom-mappings/archetypes#leaf-archetypes) [Composite archetypes](https://docs.rewire.run/custom-mappings/archetypes#composite-archetypes) [Array expansion](https://docs.rewire.run/custom-mappings/archetypes#array-expansion) [Nested fields](https://docs.rewire.run/custom-mappings/archetypes#nested-fields) [Entity keys](https://docs.rewire.run/custom-mappings/archetypes#entity-keys)

Last updated on August 26, 2026

© 2026 Rewire