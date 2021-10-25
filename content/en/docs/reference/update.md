---
title: "UPDATE"
date: 2021-10-21T14:00:44+04:00
description: >
  Update documents
---

## Synopsis

### UPDATE statement

```js {.rr}
Diagram(
  Stack(
    Sequence("UPDATE", "table-name"),
    Choice(
      0,
      Sequence("SET", OneOrMore(Sequence("field-path", "=", "expr"), ",")),
      Sequence("UNSET", OneOrMore("top-level-field", ","))
    ),
    Optional(Sequence("WHERE", "expr"), "skip")
  )
);
```
