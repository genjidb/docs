---
title: "ALTER TABLE"
date: 2021-10-22T14:00:44+04:00
description: >
  Manage tables and table constraints
---

## Synopsis

### ALTER TABLE statement

```js {.rr}
Diagram(
  Stack(
    Sequence("ALTER", "TABLE", "table-name"),
    Choice(
      0,
      Sequence("RENAME", "TO", "new-table-name"),
      Sequence("ADD", "FIELD", Link("field-definition"))
    )
  )
);
```

### Field definition

```js {.rr}
Diagram(
  Sequence("field-path"),
  OptionalSequence("type-name", OneOrMore(Link("field-constraint")))
);
```

### Field constraint

```js {.rr}
Diagram(
  Choice(
    0,
    Sequence("PRIMARY", "KEY"),
    Sequence("UNIQUE"),
    Sequence("NOT", "NULL"),
    Sequence("DEFAULT", Choice(0, Sequence("(", "expr", ")"), Sequence("expr")))
  )
);
```
