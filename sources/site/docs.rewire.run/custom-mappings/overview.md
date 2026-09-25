# Source: https://docs.rewire.run/custom-mappings/overview

Custom Mappings

# Overview

Map any ROS 2 message type to Rerun archetypes via JSON5 config.

Custom message mappings let you visualize any ROS 2 message type in Rerun without writing code. Define a JSON5 file that maps message fields to Rerun archetypes, and Rewire handles the rest.

## [Usage](https://docs.rewire.run/custom-mappings/overview#usage)

```
rewire record --mappings mappings.json5
```

## [Generating a template](https://docs.rewire.run/custom-mappings/overview#generating-a-template)

Use `rewire mappings generate` to scaffold a mappings file from the types currently discoverable on the network. Rewire inspects each topic, resolves custom message schemas, and writes a JSON5 template you can edit.

```
rewire mappings generate --output mappings.json5
```

## [Basic example](https://docs.rewire.run/custom-mappings/overview#basic-example)

```
[
  {
    type: "my_robot/MotorStatus",
    entity: "motor",
    fields: {
      temperature: { archetype: "Scalars" },
      rpm: { archetype: "Scalars" },
      status_text: { archetype: "TextLog" },
    }
  },
]
```

This maps the `my_robot/MotorStatus` message so that:

- `temperature` is logged as a `Scalars` entity
- `rpm` is logged as a `Scalars` entity
- `status_text` is logged as a `TextLog` entity

## [Mapping entry structure](https://docs.rewire.run/custom-mappings/overview#mapping-entry-structure)

Each entry in the mappings array has:

| Field | Required | Description |
| --- | --- | --- |
| `type` | Yes | Full ROS 2 message type (e.g., `my_robot/MotorStatus`) |
| `entity` | No | Custom Rerun entity path prefix |
| `fields` | Yes | Map of field name to archetype mapping |

## [Field mapping types](https://docs.rewire.run/custom-mappings/overview#field-mapping-types)

Fields can be mapped in four ways:

1. **Leaf** — single value to a simple archetype (`Scalars`, `TextLog`, `TextDocument`)
2. **Composite** — multiple sub-fields to a complex archetype (`Points3D`, `Boxes2D`, etc.)
3. **Expansion** — iterate over arrays with `each`
4. **Nested** — recurse into sub-messages with `fields`

See [Archetypes](https://docs.rewire.run/custom-mappings/archetypes) for the full list of supported archetypes and [Examples](https://docs.rewire.run/custom-mappings/examples) for real-world mapping patterns.

[Topic Filtering\\ \\ Filter topics with glob patterns for include and exclude.](https://docs.rewire.run/configuration/topic-filtering) [Archetypes\\ \\ Available Rerun archetypes for custom message mappings.](https://docs.rewire.run/custom-mappings/archetypes)

### On this page

[Usage](https://docs.rewire.run/custom-mappings/overview#usage) [Generating a template](https://docs.rewire.run/custom-mappings/overview#generating-a-template) [Basic example](https://docs.rewire.run/custom-mappings/overview#basic-example) [Mapping entry structure](https://docs.rewire.run/custom-mappings/overview#mapping-entry-structure) [Field mapping types](https://docs.rewire.run/custom-mappings/overview#field-mapping-types)

Last updated on August 26, 2026

© 2026 Rewire