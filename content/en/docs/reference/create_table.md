---
title: "CREATE TABLE"
date: 2021-10-21T14:00:44+04:00
description: >
  Define a new table
---

## Synopsis

### CREATE TABLE statement

```js {.rr}
Diagram(
  Stack(
    Sequence(
      "CREATE",
      "TABLE",
      Optional(Sequence("IF", "NOT", "EXISTS"), "skip"),
      Sequence(Link("table-name"))
    ),
    Choice(
      0,
      Sequence(
        OneOrMore(Link("field-definition"), ","),
        Optional(
          Sequence(",", OneOrMore(Link("table-constraint"), ",")),
          "skip"
        )
      ),
      Sequence(OneOrMore(Link("table-constraint"), ","))
    )
  )
);
```

#### Parameters

##### IF NOT EXISTS

Do not throw an error if a table with the same name already exists.

##### table-name

The name of the table to be created.

### Field definition

```js {.rr}
Diagram(
  Sequence(Link("field-path", "/docs/essentials/expressions/#field-path")),
  OptionalSequence(
    Link("type-name", "/docs/essentials/data-types"),
    OneOrMore(Link("field-constraint"))
  )
);
```

##### table-name

The name of the table to be created.

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

### Table constraint

```js {.rr}
Diagram(
  Choice(
    0,
    Sequence("PRIMARY", "KEY", "(", "field-path", ")"),
    Sequence("UNIQUE", "KEY", "(", "field-path", ")")
  )
);
```
