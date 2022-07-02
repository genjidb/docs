---
title: "INSERT"
date: 2021-10-21T14:00:44+04:00
description: >
  Insert documents
---

## Synopsis

### INSERT statement

```js {.rr}
Diagram(
  Stack(
    Sequence("INSERT", "INTO", "table-name"),
    Choice(0, Link("values-clause"), Link("select-stmt", "select#select-stmt")),
    Optional(Link("conflict-clause"), "skip"),
    Optional(Link("returning-clause"), "skip")
  )
);
```

### VALUES clause

```js {.rr}
Diagram(
  Stack(
    Choice(
      0,
      Sequence(
        Optional(Sequence("(", OneOrMore("field-name", ","), ")"), "skip"),
        "VALUES",
        OneOrMore(
          Sequence(
            "(",
            OneOrMore(Link("expr", "/docs/essentials/expressions"), ","),
            ")"
          ),
          ","
        )
      ),
      Sequence(
        "VALUES",
        OneOrMore(
          Link(
            "document-literal",
            "/docs/essentials/expressions#document-literal"
          ),
          ","
        )
      )
    )
  )
);
```

### Conflict clause

```js {.rr}
Diagram(
  Sequence(
    "ON",
    "CONFLICT",
    Choice(
      0,
      "IGNORE",
      "REPLACE",
      Sequence("DO", "NOTHING"),
      Sequence("DO", "REPLACE")
    )
  )
);
```

### RETURNING clause

```js {.rr}
Diagram(
  Sequence(
    "RETURNING",
    Link("expr", "/docs/essentials/expressions"),
    Optional(Sequence("AS", "alias"), "skip")
  )
);
```
