# Source: https://docs.rewire.run/custom-mappings/examples

Custom Mappings

# Examples

Example custom mapping configurations.

## [Motor telemetry](https://docs.rewire.run/custom-mappings/examples#motor-telemetry)

Log motor status as scalar time series and text logs:

```
[
  {
    type: "my_robot/MotorStatus",
    entity: "motor",
    fields: {
      temperature: { archetype: "Scalars" },
      rpm: { archetype: "Scalars" },
      current: { archetype: "Scalars" },
      status_text: { archetype: "TextLog" },
    }
  },
]
```

## [3D detections from a custom message](https://docs.rewire.run/custom-mappings/examples#3d-detections-from-a-custom-message)

Expand an array of detections into Rerun `Points3D`:

```
[
  {
    type: "my_robot/Detections",
    fields: {
      detections: {
        archetype: "Points3D",
        each: ".",
        x: "position.x",
        y: "position.y",
        z: "position.z",
        label: "class",
      },
    }
  },
]
```

## [Nested sensor data](https://docs.rewire.run/custom-mappings/examples#nested-sensor-data)

Recurse into nested sub-messages and log individual fields:

```
[
  {
    type: "my_robot/SensorPack",
    entity: "sensors",
    fields: {
      imu: {
        fields: {
          accel_x: { archetype: "Scalars" },
          accel_y: { archetype: "Scalars" },
          accel_z: { archetype: "Scalars" },
          gyro_x: { archetype: "Scalars" },
          gyro_y: { archetype: "Scalars" },
          gyro_z: { archetype: "Scalars" },
        }
      },
      gps: {
        archetype: "GeoPoints",
        lat: "latitude",
        lon: "longitude",
        alt: "altitude",
      },
    }
  },
]
```

## [Bounding boxes from a vision pipeline](https://docs.rewire.run/custom-mappings/examples#bounding-boxes-from-a-vision-pipeline)

Map 2D detection results to `Boxes2D`:

```
[
  {
    type: "my_robot/BoundingBoxes",
    fields: {
      boxes: {
        archetype: "Boxes2D",
        each: ".",
        x: "center_x",
        y: "center_y",
        w: "width",
        h: "height",
        label: "class_name",
      },
    }
  },
]
```

## [Combining with built-in types](https://docs.rewire.run/custom-mappings/examples#combining-with-built-in-types)

Custom mappings are additive — they work alongside the 74 built-in type mappings. If a topic's type has both a built-in converter and a custom mapping, the custom mapping takes precedence.

[Archetypes\\ \\ Available Rerun archetypes for custom message mappings.](https://docs.rewire.run/custom-mappings/archetypes) [Supported Types\\ \\ The built-in ROS 2 to Rerun type mappings.](https://docs.rewire.run/supported-types)

### On this page

[Motor telemetry](https://docs.rewire.run/custom-mappings/examples#motor-telemetry) [3D detections from a custom message](https://docs.rewire.run/custom-mappings/examples#3d-detections-from-a-custom-message) [Nested sensor data](https://docs.rewire.run/custom-mappings/examples#nested-sensor-data) [Bounding boxes from a vision pipeline](https://docs.rewire.run/custom-mappings/examples#bounding-boxes-from-a-vision-pipeline) [Combining with built-in types](https://docs.rewire.run/custom-mappings/examples#combining-with-built-in-types)

Last updated on August 26, 2026

© 2026 Rewire