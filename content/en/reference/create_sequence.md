---
title: "CREATE SEQUENCE"
date: 2021-10-22T14:00:44+04:00
description: >
  Define a new sequence
---

## Synopsis

### create-sequence-stmt

```railroad
Diagram(
  Stack(
    Sequence(
      "CREATE",
      "SEQUENCE",
      Optional(Sequence("IF", "NOT", "EXISTS"), "skip"),
      "sequence-name"
    ),
    Optional(
      MultipleChoice(
        0,
        "any",
        Sequence("AS", "type-name"),
        Sequence("INCREMENT", Optional("BY", "skip"), "integer"),
        Sequence(
          "NO",
          Choice(
            0,
            Sequence("MINVALUE"),
            Sequence("MAXVALUE"),
            Sequence("CYCLE")
          )
        ),
        Sequence("MINVALUE", "integer"),
        Sequence("MAXVALUE", "integer"),
        Sequence("START", Optional("WITH", "skip"), "integer"),
        Sequence("CACHE", "integer"),
        Sequence("CYCLE"),
      ),
      "skip"
    )
  )
);
```
