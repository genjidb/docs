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

Here is a list of expressions supported by Genji.

## Literal expressions

Any [literal]({{< relref "/docs/genji-sql/lexical-structure" >}}#literals) evaluates to the closest compatible [type]({{< relref "/docs/genji-sql/data-types" >}}#fixed-size-data-types).

### Strings

[Strings]({{< relref "/docs/genji-sql/lexical-structure" >}}#literals) are evaluated to the [`TEXT`]({{< relref "/docs/genji-sql/data-types" >}}#variable-size-data-types) type, which are utf-8 encoded.

### Integers

[Integers]({{< relref "/docs/genji-sql/lexical-structure" >}}#integers) are evaluated into the [INTEGER]({{< relref "/docs/genji-sql/data-types" >}}#fixed-size-data-types) type.

If an integer is bigger than the maximum value of a 64 bit integer or smaller than the minimum 64 bit integer value, it will be evaluated as a `DOUBLE`.

### Floats

[Floats]({{< relref "/docs/genji-sql/lexical-structure" >}}#floats) are evaluated into the [`DOUBLE`]({{< relref "/docs/genji-sql/data-types" >}}#fixed-size-data-types) type.

### Booleans

[Booleans]({{< relref "/docs/genji-sql/lexical-structure" >}}#booleans) are evaluated into the [`BOOL`]({{< relref "/docs/genji-sql/data-types" >}}#fixed-size-data-types) type.

### Arrays

[Arrays]({{< relref "/docs/genji-sql/lexical-structure" >}}#arrays) are evaluated into the [`ARRAY`]({{< relref "/docs/genji-sql/data-types" >}}#variable-size-data-types) type.

### Documents

[Documents]({{< relref "/docs/genji-sql/lexical-structure" >}}#documents) are evaluated into the [`DOCUMENT`]({{< relref "/docs/genji-sql/data-types" >}}#variable-size-data-types) type.

### Paths

[Paths]({{< relref "/docs/genji-sql/lexical-structure" >}}#paths) are evaluated into the value they refer to.
They are used to select a value from a [document]({{< relref "/docs/genji-sql/documents" >}}).
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

Here are examples on how dot notation is evaluated:

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
```

## Operators

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

| Name   | Description                                                                                                                      |
| ------ | -------------------------------------------------------------------------------------------------------------------------------- |
| =      | Evaluates to `true` if operands are equal, otherwise returns `false`                                                             |
| !=     | Evaluates to `true` if operands are not equal, otherwise returns `false`                                                         |
| >      | Evaluates to `true` if the left-side expression is greater than the right-side expression, otherwise returns `false`             |
| >=     | Evaluates to `true` if the left-side expression is greater than or equal to the right-side expression, otherwise returns `false` |
| <      | Evaluates to `true` if the left-side expression is less than the right-side expression, otherwise returns `false`                |
| <=     | Evaluates to `true` if the left-side expression is less than or equal to the right-side expression, otherwise returns `false`    |
| IN     | Evaluates to `true` if the left-side expression is equal to one of the values of the right-side array                            |
| NOT IN | Evaluates to `false` if the left-side expression is equal to one of the values of the right-side array                           |
| IS     | Has the same behaviour as `=` except that it returns `true` if both operands are `NULL`                                          |
| IS NOT | Has the same behaviour as `!=` except that it supports comparing with `NULL`                                                     |

Examples:

```python
1 = 1
-> true

1 > 2.5
-> false

3 IN [1, 2, 3]
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
| ---- | ---------------------------------------------------------- | ---------- |
| `+`  | Adding two values                                          |
| `-`  | Substracting two values                                    |
| `*`  | Multiplying two values                                     |
| `/`  | Dividing two values                                        |
| `%`  | Find the remainder after division of one number by another |
| `&`  | Bitwise AND                                                |
| `    | `                                                          | Bitwise OR |
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

### Evaluation tree and precedence

When parsed, an expression is turned into an evaluation tree so it is possible to combine operators to form complex expressions.
The order in which these expressions are executed depends on the priority of the operator.

Here is the list of operators ordered by ascending precedence. Operators with higher precedence are executed before the ones with lower precedence

- `OR`
- `AND`
- `=`, `!=`, `<`, `<=`, `>`, `>=`
- `+`, `-`, `|`, `^`
- `*`, `/`, `%`, `&`

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
