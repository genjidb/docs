---
title: "DROP TABLE"
date: 2021-10-21T14:00:44+04:00
description: >
  Delete a table and all of its content
---

## Synopsis

### DROP TABLE statement

```js {.rr}
Diagram(
  Sequence(
    "DROP",
    "TABLE",
    Optional(Sequence("IF", "EXISTS"), "skip"),
    "table-name"
  )
);
```
