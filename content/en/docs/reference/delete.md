---
title: "DELETE"
date: 2021-10-21T14:00:44+04:00
description: >
  Delete documents
---

## Synopsis

### DELETE statement

```js {.rr}
Diagram(
  Stack(
    Sequence("DELETE", "FROM", "table-name"),
    Optional(Sequence("WHERE", "expr"), "skip"),
    Optional(
      Sequence(
        "ORDER",
        "BY",
        "expr",
        Optional(Choice(0, "ASC", "DESC"), "skip")
      ),
      "skip"
    ),
    Optional(Sequence("LIMIT", "expr"), "skip"),
    Optional(Sequence("OFFSET", "expr"), "skip")
  )
);
```
