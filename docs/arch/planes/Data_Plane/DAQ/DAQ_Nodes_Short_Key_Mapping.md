Instead of a simple array, the payload contains a JSON object (a map or dictionary) where the keys are short, predictable strings ("0", "1", or "c0", "c1", etc.) that map to the full UUIDs.

Instead of using Full UUIDs like:

```json
{
  "ts": 1693827785123456789,
  "values": {
    "a1b2c3d4-....": 5.2,
    "b2c3d4e5-....": 10.4
  }
}
```

Use a device map

```json
{
  "ts": 1693827785123456789,
  "map_ver": "device_map_v2",
  "values": {
    "c0": 5.2,
    "c14": 10.4
  }
}
```

- **Payload Size**:	Very Small. Slightly larger due to keys ("c0":), but still a huge saving over UUIDs.
- **Flexibility**	Excellent. Missing channel values can simply be omitted from the object, which is cleaner and more robust.
- **Complexity**	Moderate. Requires a map to interpret.

The advantage of this pattern is how it handles missing data. If a sensor for channel 2 doesn't report a value in a given cycle, the DAQ node can just leave it out of the payload.

