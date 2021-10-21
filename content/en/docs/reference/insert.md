---
title: "INSERT"
date: 2021-10-21T14:00:44+04:00
description: >
  Insert documents
---

## Synopsis

### insert-stmt

```railroad
Diagram(
  Stack(
    Sequence("INSERT", "INTO", "table-name"),
    Choice(0, {{% rrlink "values-clause" %}}, {{% rrlink "select-stmt" "select#select-stmt" %}}),
    Optional({{% rrlink "conflict-clause" %}}, "skip"),
    Optional({{% rrlink "returning-clause" %}}, "skip")
  )
);
```

### values-clause

```railroad
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
      Sequence("VALUES", OneOrMore({{% rrlink "document-literal" %}}, ",")),
    )
  )
);
```

### document-literal

```railroad
Diagram(
  "{",
  OneOrMore(Sequence(Choice(0, "identifier", "string"), ":", "expr"), ","),
  "}"
);
```

### conflict-clause

```railroad
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

### returning-clause

```railroad
Diagram(
  Sequence("RETURNING", "expr", Optional(Sequence("AS", "alias"), "skip"))
);
```

```railroad
Diagram(
  Stack(
    Sequence("INSERT", "INTO", "table-name"),
    Choice(
      0,
      Sequence(
        "(",
        OneOrMore("field-name", ","),
        ")",
        "VALUES",
        OneOrMore(Sequence("(", OneOrMore("expr", ","), ")"), ",")
      ),
      Sequence("VALUES", OneOrMore({{% rrlink "document-literal" %}}, ",")),
      {{% rrlink "select-stmt" "select#select-stmt" %}}
    ),
    Optional(
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
      ),
      "skip"
    ),
    Optional(
      Sequence("RETURNING", "expr", Optional(Sequence("AS", "alias"), "skip")),
      "skip"
    )
  )
);
```
