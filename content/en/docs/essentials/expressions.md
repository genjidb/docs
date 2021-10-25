---
title: "Expressions"
date: 2020-04-03T12:36:25+04:00
weight: 40
description: >
  How expression are evaluated, compared, etc.
---

Expressions are components that can be evaluated to a value.

Example:

```python
1 + 1 # expression
-> 2  # result
```

An expression can be found in two forms:

- **unary**: meaning it contains only one component
- **binary**: meaning it contains two expressions, or _operands_, and one _operator_. i.e. `<expr> <operator> <expr>`

Example:

```sql
/* Unary expressions */
1
name
"foo"

/* Binary expressions */
age >= 18
1 AND 0
```

Here is a list of all supported expressions:

```js {.rr}
Diagram(
  Choice(
    0,
    Link("literal-value"),
    Link("parameter"),
    Link("field-path"),
    Sequence("NOT", "expr"),
    Sequence("expr", Link("binary-operator", "#operators"), "expr"),
    Sequence(
      "expr",
      Optional("NOT"),
      Choice(
        0,
        Link("IN", "#comparison-operators"),
        Link("LIKE", "#comparison-operators")
      ),
      "expr"
    ),
    Sequence(
      "expr",
      Link("IS", "#comparison-operators"),
      Optional("NOT"),
      "expr"
    ),
    Sequence(
      "expr",
      Optional("NOT"),
      Link("BETWEEN", "#comparison-operators"),
      "expr",
      "AND",
      "expr"
    ),
    Sequence(Link("function", "#functions"), "(", OneOrMore("expr", ","), ")"),
    Sequence("CAST", "(", "expr", "AS", "type-name", ")"),
    Sequence("NEXT", "VALUE", "FOR", "sequence-name"),
    Sequence("(", "expr", ")")
  )
);
```

## Literal value

Any literal evaluates to the closest compatible [type]({{< relref "/docs/essentials/data-types" >}}#fixed-size-data-types).

```js {.rr}
Diagram(
  Choice(
    0,
    Link("integer-literal"),
    Link("number-literal"),
    Link("string-literal"),
    Link("blob-literal"),
    Link("bool-literal"),
    Link("array-literal"),
    Link("document-literal"),
    "NULL"
  )
);
```

### Integer literal

```js {.rr}
Diagram(Sequence(Optional(Choice(0, "+", "-")), OneOrMore("digit")));
```

An integer literal is a sequence of characters that only contain digits. They may start with a `+` or `-` sign.

```sql
123456789
+100
-455
```

Integer literals are evaluated into the [`INTEGER`]({{< relref "/docs/essentials/data-types" >}}#fixed-size-data-types) type.

If an integer is bigger than the maximum value of a 64 bit integer or smaller than the minimum 64 bit integer value, it will be evaluated as a [`DOUBLE`]({{< relref "/docs/essentials/data-types" >}}#fixed-size-data-types).

### Number literal

```js {.rr}
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

A number literal is a sequence of characters that contains three parts:

- a sequence of digits
- a decimal point \(i.e. `.`\)
- a sequence of digits

They may start with a `+` or a `-` sign.

```sql
123.456
+3.14
-1.0
```

Number literals are evaluated to the [`DOUBLE`]({{< relref "/docs/essentials/data-types" >}}#fixed-size-data-types) type.

### String literal

```js {.rr}
Diagram(
  Choice(
    0,
    Sequence('"', ZeroOrMore("unicode-character"), '"'),
    Sequence("'", ZeroOrMore("unicode-character"), "'")
  )
);
```

A string literal is a sequence of utf-8 encoded characters surrounded by double or single quotes. They may contain any unicode character or escaped single or double quotes \(i.e `\'` or `\"`\).

```sql
"l'école des fans"
'(╯ಠ_ಠ）╯︵ ┳━┳'
'foo \''
```

String literals are evaluated to the [`TEXT`]({{< relref "/docs/essentials/data-types" >}}#variable-size-data-types) type.

### Blob literal

```js {.rr}
Diagram(
  Choice(
    0,
    Sequence('"\\x', ZeroOrMore("hexadecimal-character"), '"'),
    Sequence("'\\x", ZeroOrMore("hexadecimal-character"), "'")
  )
);
```

A blob literal starts with `\x` followed by a list of hexadecimal characters which is then decoded into raw bytes by Genji.

Blob literals are evaluated to the [`BLOB`]({{< relref "/docs/essentials/data-types" >}}#variable-size-data-types) type.

### Bool literal

```js {.rr}
Diagram(Choice(0, "TRUE", "FALSE"));
```

A boolean literal is any sequence of character that is written as `true` or `false`, regardless of the case.

```sql
true
false
TRUE
FALSE
tRUe
FALse
```

Boolean literals are evaluated into the [`BOOL`]({{< relref "/docs/essentials/data-types" >}}#fixed-size-data-types) type.

### Array literal

```js {.rr}
Diagram(
  Choice(
    0,
    Sequence("[", OneOrMore("expr", ","), "]"),
    Sequence("(", OneOrMore("expr", ","), ")")
  )
);
```

An array literal is any sequence of character that starts and ends with either:

- `[` and `]`
- `(` and `)`

and that contains a coma-separated list of expressions.

```python
[1.5, "hello", 1 > 10, [true, -10], {foo: "bar"}]
```

Array literals are evaluated into the [`ARRAY`]({{< relref "/docs/essentials/data-types" >}}#variable-size-data-types) type.

### Document literal

```js {.rr}
Diagram(
  "{",
  OneOrMore(Sequence(Choice(0, "identifier", "string"), ":", "expr"), ","),
  "}"
);
```

A document is any sequence of character that starts and ends with `{` and `}` and that contains a list of pairs.
Each pair associates an identifier with an [expression]({{< relref "/docs/essentials/expressions" >}}), both separated by a colon. Each pair must be separated by a coma.

```js
{
    foo: 1,
    bar: "hello",
    baz: true AND false,
    "long field": {
        a: 10
    }
}
```

In a document, the identifiers are referred to as **fields**.
In the example above, the document has four top-level fields (`foo`, `bar`, `baz` and `long field`) and one nested field `a`.

Note that any JSON object is a valid document.

Document literals are evaluated into the [`DOCUMENT`]({{< relref "/docs/essentials/data-types" >}}#variable-size-data-types) type.

## Identifier

```js {.rr}
Diagram(
  Choice(
    0,
    OneOrMore(Choice(0, "ascii-letter", "digit", "_")),
    Sequence("`", OneOrMore("unicode-character"), "`")
  )
);
```

Identifiers are a sequence of characters that refer to table names, field names and index names.

Identifiers may be unquoted or surrounded by backquotes. Depending on that, different rules may apply.

| Unquoted identifiers                                                       | Identifiers surrounded by backquotes                                             |
| -------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Must begin with an uppercase or lowercase ASCII character or an underscore | May contain any unicode character, other than the new line character (i.e. `\n`) |
| May contain only ASCII letters, digits and underscore                      | May contain escaped `"` character (i.e. `\"`)                                    |

```text
foo
_foo_123_
`頂きます (*｀▽´)_旦~~`
`foo \` bar`
```

## Field path

```js {.rr}
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

A field path is any sequence of characters that contains one or more [identifiers](#identifier) separated by dots or square brackets.

```python
foo
foo.bar[10]
foo["long field"][0].bat.`other long field`
```

Depending on the context, a single identifier with no dot or square bracket will be parsed as an [identifier](#identifier) or as a field path.

Field paths are evaluated into the value they refer to.
They are used to select a value from a [document]({{< relref "/docs/essentials/documents" >}}).
Their type will depend on the type of the value extracted from the document.

Given the following document:

```js
{
    "recipes": 10,
    "cooking-time": {
        "eggs": [3, 6, 9]
    },
}
```

Here are examples on how field paths are evaluated:

```python
recipes
-> 10

`cooking-time`
->  {
        "eggs": [
            3,
            6,
            9
        ]
    }

`cooking-time`.eggs[2]
-> 9

`cooking-time`.eggs[10]
-> NULL

```

## Parameter

```js {.rr}
Diagram(Choice(0, "?", "$identifier"));
```

A parameters is an expressions used to represent a value passed when the query is evaluated.
Genji supports two types of parameters:

- Positional parameters: `?`
- Named parameters: `$` followed by an [identifier](#identifier)

## Functions

```js {.rr}
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

A function name is an expression that represent a builtin function.
It can either represent a global function or a function within a package.

```python
count()
typeof("hello")
math.atan2(1.1, 1.1)
```

## Operators

```js {.rr}
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

Genji provides a list of operators that can be used to compute operations with expressions.
Operators are binary expressions, meaning they always take exactly two operands.
It is possible though to combine multiple operators to create an [evaluation tree](#evaluation-tree-and-precedence).

### Logical operators

Logical operators are operators that return a boolean under certain conditions.

| Name  | Description                                                              |
| ----- | ------------------------------------------------------------------------ |
| `AND` | Evaluates to `true` if both operands are _truthy_                        |
| `OR`  | Evaluates to `true` if either the left operand or the right are _truthy_ |

An expression is _truthy_ if it evaluates to a non zero-value of its type.

### Comparison operators

These operators are used to compare values and evaluate to a boolean.

| Name    | Description                                                                                                                      |
| ------- | -------------------------------------------------------------------------------------------------------------------------------- |
| =       | Evaluates to `true` if operands are equal, otherwise returns `false`                                                             |
| !=      | Evaluates to `true` if operands are not equal, otherwise returns `false`                                                         |
| >       | Evaluates to `true` if the left-side expression is greater than the right-side expression, otherwise returns `false`             |
| >=      | Evaluates to `true` if the left-side expression is greater than or equal to the right-side expression, otherwise returns `false` |
| <       | Evaluates to `true` if the left-side expression is less than the right-side expression, otherwise returns `false`                |
| <=      | Evaluates to `true` if the left-side expression is less than or equal to the right-side expression, otherwise returns `false`    |
| IN      | Evaluates to `true` if the left-side expression is equal to one of the values of the right-side array                            |
| NOT IN  | Evaluates to `false` if the left-side expression is equal to one of the values of the right-side array                           |
| IS      | Has the same behaviour as `=` except that it returns `true` if both operands are `NULL`                                          |
| IS NOT  | Has the same behaviour as `!=` except that it supports comparing with `NULL`                                                     |
| BETWEEN | Evaluates to `true` if the left-side expression is between the two boundaries                                                    |

Examples:

```python
1 = 1
-> true

1 > 2.5
-> false

3 IN [1, 2, 3]
-> true

5 BETWEEN 2 AND 10
-> true
```

#### Conversion during comparison

Prior to comparison, an implicit conversion is operated for the operands to be of the same type.
Not all types can be compared together. When two incompatible types are compared, the comparison always returns `false`,
except if one of the operands is NULL, in that case it returns NULL.

Example:

```python
1 > "hello"
-> false
```

```python
1 < "hello"
-> false
```

The comparison follows a list of rules that are executed in order:

- If one of the operands is NULL, return `NULL`.
- If both operands are documents, use the [Comparing documents](#comparing-documents) rule
- If both operands are arrays, use the [Comparing arrays](#comparing-arrays) rule
- If both operands are numbers (INTEGER or DOUBLE), cast the integer to DOUBLE then compare them together.
- If both operands have the same type, compare them together.

In any other case, return `false`.

#### Comparing documents

The fields of each document are sorted, then they are compared one by one, until they are found not equal. The comparison is then determined by the result of the comparison between these two values.
If both keys are equal, compare the values.

```python
{a: 1, b: 2} = {b: 2, a: 1}
-> true

{} = {}
-> true

{a: 1, b: 3} > {a: 1, b: 2}
-> true

{a: 100} > {aa: 1}
-> false
```

#### Comparing arrays

Each elements of both arrays are compared one by one, index by index, until they are found not equal. The comparison is then determined by the result of the comparison between these two values.

```python
[1, 2, 3] > [1, 1 + 1, 1]
-> true
```

Let's break down the example above:

1. Index 0: `1` and `1` are equal, the comparison continues
2. Index 1: `2` and `1 + 1` are equal, the comparison continues
3. Index 2: `3` is greater then `1`, the comparison stops and the first array is considered greater than the second one

Two empty arrays are considered equal:

```python
[] = []
-> true
```

The size of arrays doesn't matter, unless all the elements of the smallest one are equal to the other one. In that case the biggest array is considered greater.

```python
[3] > [1, 100000]
-> true

[1, 2] < [1, 2, 3]
-> true
```

### Arithmetic operators

| Name | Description                                                |
| ---- | ---------------------------------------------------------- |
| `+`  | Adding two values                                          |
| `-`  | Substracting two values                                    |
| `*`  | Multiplying two values                                     |
| `/`  | Dividing two values                                        |
| `%`  | Find the remainder after division of one number by another |
| `&`  | Bitwise AND                                                |
| `\|` | Bitwise OR                                                 |
| `^`  | Bitwise XOR                                                |

Arithmetic operations are supported only for the following types:

- `INTEGER`
- `DOUBLE`

Note that `INTEGER` and `DOUBLE` types can be calculated together, in that case `INTEGER` values will be converted to `DOUBLE` prior the operation.

Any usage of these operators on incompatible types will return `NULL`.

```python
3 + 3.5
-> 6.5

3 + '1'
-> NULL
```

#### The case of NULL

Any arithmetic operation with one of the operand being `NULL` returns `NULL`.

```python
NULL + 1
-> NULL

5 * 10 - NULL
-> NULL
```

#### Division rules

The division obeys a few rules depending on the types of the operands:

- Dividing two integers, always results in an integer
- Dividing by zero, returns `NULL`

#### Return type and overflow

The type of the result of an operation doesn't necessarily match the type of the operands.

- The result of a DOUBLE operation will always return a DOUBLE
- The result of an INTEGER operation will return an INTEGER, unless the return value is bigger than the maximum value of 64-bit integer. In that case, the return type will be a DOUBLE

### Other operators

| Name   | Description                        |
| ------ | ---------------------------------- |
| `\|\|` | Concatenation of two `TEXT` values |

### Evaluation tree and precedence

When parsed, an expression is turned into an evaluation tree so it is possible to combine operators to form complex expressions.
The order in which these expressions are executed depends on the priority of the operator.

Here is the list of operators ordered by ascending precedence. Operators with higher precedence are executed before the ones with lower precedence

- `OR`
- `AND`
- `=`, `!=`, `<`, `<=`, `>`, `>=`
- `+`, `-`, `|`, `^`
- `*`, `/`, `%`, `&`
- `||`

Example:

```sql
3 + 4 * 2 > 10 AND 2 - 2 = false
-> true
```

This expression can be represented as the following tree:

```text
.
└── AND
    ├── >
    │   ├── +
    │   │   ├── 3
    │   │   └── *
    │   │       ├── 4
    │   │       └── 2
    │   └── 10
    └── -
        ├── 2
        └── 2
```

The deepest branches will be executed first, recursively until reaching the root.
