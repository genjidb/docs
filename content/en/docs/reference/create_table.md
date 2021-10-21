---
title: "CREATE TABLE"
date: 2021-10-21T14:00:44+04:00
description: >
  Define a new table
---

## Synopsis

### create-table-stmt

```railroad
Diagram(
  Stack(
    Sequence(
      "CREATE",
      "TABLE",
      Optional(Sequence("IF", "NOT", "EXISTS"), "skip"),
      Sequence("table-name")
    ),
    Choice(
      0,
      Sequence(
        OneOrMore({{% rrlink "field-definition" %}}, ","),
        Optional(
          Sequence(",", OneOrMore({{% rrlink "table-constraint" %}}, ",")),
          "skip"
        )
      ),
      Sequence(OneOrMore({{% rrlink "table-constraint" %}}, ","))
    )
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

### table-constraint

```railroad
Diagram(
  Choice(
    0,
    Sequence("PRIMARY", "KEY", "(", "field-path", ")"),
    Sequence("UNIQUE", "KEY", "(", "field-path", ")")
  )
);
```
