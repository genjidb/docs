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
        "(",
        OneOrMore("field-name", ","),
        ")",
        "VALUES",
        OneOrMore(Sequence("(", OneOrMore("expr", ","), ")"), ",")
      ),
      Sequence("VALUES", OneOrMore(Link("document-literal"), ","))
    )
  )
);
```

### Document literal

```js {.rr}
Diagram(
  "{",
  OneOrMore(Sequence(Choice(0, "identifier", "string"), ":", "expr"), ","),
  "}"
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
  Sequence("RETURNING", "expr", Optional(Sequence("AS", "alias"), "skip"))
);
```
