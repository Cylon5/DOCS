## **Design principle**

Route by stable `channel_id`; keep human-readable names (aliases) in metadata.


- **Processed/derived (per channel)**  
  `processed.<site>.<area>.<pipeline>.ch.<channel_id>.<calc>`

```mermaid
---
title: Subject Stream Tag Syntax
---
graph LR
    subgraph Measurements Stream Tag
        direction LR
            START3@{ shape: start }
            START3-->AAA(processed)-->p1(.)
            p1-->BBB([site])-->p2(.)
            p2-->CCC([area])-->p3(.)
            p3-->DDD([channel])-->p4(.)
            p4-->EEE([channel_id])-->p5(.)
            p5-->FFF([specifier])-->STOP3@{shape: stop}
            EEE-->STOP3
            FFF-->p5
    end
    subgraph Legend
        direction TB
            lit(Literal)
            tok([Token])
    end
```