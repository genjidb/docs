---
title: "Expressions"
date: 2021-10-22T14:00:44+04:00
description: >
  Expressions and literals
---

## Synopsis

### expr

```railroad
Diagram(
  Choice(
    0,
    "literal-value",
    "parameter",
    "field-path",
    Sequence("NOT", "expr"),
    Sequence("expr", "binary-operator", "expr"),
    Sequence("expr", Optional("NOT"), Choice(0, "IN", "LIKE"), "expr"),
    Sequence("expr", "IS", Optional("NOT"), "expr"),
    Sequence("expr", Optional("NOT"), "BETWEEN", "expr", "AND", "expr"),
    Sequence("function-name", "(", OneOrMore("expr", ","), ")"),
    Sequence("CAST", "(", "expr", "AS", "type-name", ")"),
    Sequence("NEXT", "VALUE", "FOR", "sequence-name"),
    Sequence("(", "expr", ")")
  )
);
```

### literal-value

```railroad
Diagram(
  Choice(
    0,
    "integer-literal",
    "number-literal",
    "string-literal",
    "blob-literal",
    "bool-literal",
    "array-literal",
    "document-literal",
    "NULL"
  )
);
```

### parameter

```railroad
Diagram(Choice(0, "?", "$N"))
```

### field-path

```railroad
Diagram(
  Sequence(
    "identifier",
    Optional(
      OneOrMore(
        Choice(
          0,
          Sequence(".", "identifier"),
          Sequence("[", "integer-literal", "]"),
          Sequence("[", "string-literal", "]")
        ),
        ","
      ),
      "skip"
    )
  )
);
```

### binary-operator

```railroad
Diagram(
  Choice(
    0,
    "||",
    "*",
    "/",
    "%",
    "+",
    "-",
    "|",
    "&",
    "^",
    ">",
    ">=",
    "<",
    "<=",
    "=",
    "!=",
    "IS",
    "IN",
    "LIKE",
    "AND",
    "OR"
  )
);
```

### function-name

```railroad
Diagram(
  Sequence(
    "identifier",
    Optional(Sequence(".", "identifier"), "skip"),
    "(",
    ZeroOrMore("expr", ","),
    ")"
  )
);
```

### integer-literal

```railroad
Diagram(Sequence(Optional(Choice(0, "+", "-")), OneOrMore("digit")));
```

### number-literal

```railroad
Diagram(
  Sequence(
    Choice(
      0,
      Sequence(OneOrMore("digit")),
      Sequence(OneOrMore("digit"), ".", OneOrMore("digit")),
      Sequence(".", OneOrMore("digit"))
    ),
    Optional(
      Sequence(
        Choice(0, "e", "E"),
        Optional(Choice(0, "+", "-")),
        OneOrMore("digit")
      )
    )
  )
);
```

### string-literal

```railroad
Diagram(
  Choice(
    0,
    Sequence('"', ZeroOrMore("unicode-character"), '"'),
    Sequence("'", ZeroOrMore("unicode-character"), "'")
  )
);
```

### blob-literal

```railroad
Diagram(
  Choice(
    0,
    Sequence('"\\x', ZeroOrMore("hexadecimal-character"), '"'),
    Sequence("'\\x", ZeroOrMore("hexadecimal-character"), "'")
  )
);
```

### bool-literal

```railroad
Diagram(
  Choice(
    0,
    "TRUE",
    "FALSE"
  )
);
```

### array-literal

```railroad
Diagram(
  Choice(
    0,
    Sequence("[", OneOrMore("expr", ","), "]"),
    Sequence("(", OneOrMore("expr", ","), ")")
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

### identifier

```railroad
Diagram(OneOrMore(Choice(0, "ascii-letter", "digit", "_")));
```
