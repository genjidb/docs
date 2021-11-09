---
title: "SELECT"
date: 2021-10-21T14:00:44+04:00
description: >
  Query the database
---

## Synopsis

### SELECT statement

```js {.rr}
Diagram(
  Stack(
    OneOrMore(
      Group(
        Stack(
          Sequence(
            "SELECT",
            Optional("DISTINCT", "skip"),
            OneOrMore(Link("result-field"), ",")
          ),
          Optional(Sequence("FROM", Link("table-name")), "skip"),
          Optional(
            Sequence("WHERE", Link("expr", "/docs/essentials/expressions")),
            "skip"
          ),
          Optional(
            Sequence(
              "GROUP",
              "BY",
              Link("expr", "/docs/essentials/expressions")
            ),
            "skip"
          )
        ),
        "select-core"
      ),
      Choice(0, "UNION", Sequence("UNION", "ALL"))
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

### Result field

```js {.rr}
Diagram(
  Choice(
    0,
    Sequence(
      Link("expr", "/docs/essentials/expressions"),
      Optional(Sequence("AS", "field-alias"), "skip")
    ),
    "*"
  )
);
```
