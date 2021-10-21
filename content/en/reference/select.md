---
title: "SELECT"
date: 2021-10-21T14:00:44+04:00
description: >
  Query the database
---

## Synopsis

### select-stmt

```railroad
Diagram(
  Stack(
    OneOrMore(
      Group(
        Stack(
          Sequence(
            "SELECT",
            Optional("DISTINCT", "skip"),
            OneOrMore({{% rrlink "result-field" %}}, ",")
          ),
          Optional(Sequence("FROM", "table-name"), "skip"),
          Optional(Sequence("WHERE", "expr"), "skip"),
          Optional(Sequence("GROUP", "BY", "expr"), "skip")
        ),
        "select-core"
      ),
      Choice(0, "UNION", Sequence("UNION", "ALL"))
    ),
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

### result-field

```railroad
Diagram(
  Choice(
    0,
    Sequence("expr", Optional(Sequence("AS", "field-alias"), "skip")),
    "*"
  )
);
```
