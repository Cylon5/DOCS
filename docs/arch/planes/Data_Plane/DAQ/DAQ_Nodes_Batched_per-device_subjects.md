## **Design principle**
In this model, you send one message per device per timestamp (or acquisition cycle), containing the values for all channels on that device.

- **Subject:** `measurements.preprocessed.site.node.device.<device_id>` (Note the `preprocessed` in the subject to distinguish the stream).

- **Example Payload:**
 
```json
    {
      "ts": 1693827785123456789,
      "values": {
        "ch0": 5.2,
        "ch1": 10.4,
        "ch2": -3.1,
        "...": "...",
        "ch47": 8.7
      }
    }
```

## **Pros**

- **Massively Reduced Message Rate** For a device sampling at 1kHz, this is only 1,000 messages per second, not 48,000.

- **Network Efficiency:** The protocol overhead is much lower relative to the payload size.

- **Guaranteed Time Correlation:** All values in the values object are from the same acquisition cycle. This is critical for any analysis that compares channels (e.g., calculating power from voltage and current).

## **Cons**

- **Over-fetching for Consumers:** A client that only wants ch1 still receives the data for all 47 other channels.