---
title: "ALTER TABLE"
date: 2021-10-22T14:00:44+04:00
description: >
  Manage tables and table constraints
---

## Synopsis

### alter-table-stmt

```railroad
Diagram(
  Stack(
    Sequence("ALTER", "TABLE", "table-name"),
    Choice(
      0,
      Sequence("RENAME", "TO", "new-table-name"),
      Sequence("ADD", "FIELD", {{% rrlink "field-definition" %}})
    ),
  )
);
```

### field-definition

```railroad
Diagram(
  Sequence("field-path"),
  OptionalSequence(
    "type-name",
    OneOrMore({{% rrlink "field-constraint" %}})
  ),
);
```

### field-constraint

```railroad
Diagram(
  Choice(0,
    Sequence("PRIMARY", "KEY"),
    Sequence("UNIQUE"),
    Sequence("NOT", "NULL"),
    Sequence("DEFAULT", Choice(0, Sequence("(", "expr", ")"), Sequence("expr")))
  )
);
```
