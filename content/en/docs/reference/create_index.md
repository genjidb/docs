---
title: "CREATE INDEX"
date: 2021-10-22T14:00:44+04:00
description: >
  Define a new index
---

## Synopsis

### CREATE INDEX statement

```js {.rr}
Diagram(
  Stack(
    Sequence("CREATE", Optional("UNIQUE", "skip"), "INDEX"),
    Choice(
      0,
      Optional("index-name"),
      Sequence("IF", "NOT", "EXISTS", "index-name")
    ),
    Sequence("ON", "table-name", "(", OneOrMore("field-path", ","), ")")
  )
);
```
