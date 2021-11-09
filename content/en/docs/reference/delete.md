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
    Optional(
      Sequence("WHERE", Link("expr", "/docs/essentials/expressions")),
      "skip"
    ),
    Optional(
      Sequence(
        "ORDER",
        "BY",
        Link("expr", "/docs/essentials/expressions"),
        Optional(Choice(0, "ASC", "DESC"), "skip")
      ),
      "skip"
    ),
    Optional(
      Sequence("LIMIT", Link("expr", "/docs/essentials/expressions")),
      "skip"
    ),
    Optional(
      Sequence("OFFSET", Link("expr", "/docs/essentials/expressions")),
      "skip"
    )
  )
);
```
