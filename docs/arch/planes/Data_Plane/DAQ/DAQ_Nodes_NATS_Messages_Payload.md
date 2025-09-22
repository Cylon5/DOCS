To minimize network bandwidth, especially with a high channel count, the use of the [Short Key Mapping Pattern](DAQ_Nodes_Short_Key_Mapping.md) is proposed.

Instead of sending long UUIDs as keys in every message, we replace them with short, stable identifiers (e.g., "c0", "c1"). The mapping from the short key to the full `channel_id` is managed centrally.

## Payload Structure: Device Message


``` json
{
  "ts": 1693563330.123,
  "map_ver": "device_map_v3",
  "values": {
    "c0": { "metrics": { "avg": 5.2, "max": 8.1, "min": 2.3 },"win_ms": 1000 },
    "c1": { "metrics": { "avg": 20.7, "max": 25.0, "min": 15.5 },"win_ms": 1000}
  }
}
```
the payload represents that for a given timestamp and window, a channel produced a whole set of metric values.

## Example Channel Map (`device_map_v3.json`):

versionned channel map that corresponds to the `device_id`

``` json
{
  "c0": "a1b2c3d4-e5f6-4a5b-8c9d-0123456789ab",
  "c1": "b2c3d4e5-f6a7-4b5c-9d8e-123456789abc",
  "c2": "c3d4e5f6-a7b8-4c5d-8e9f-23456789abcd"
}
```
