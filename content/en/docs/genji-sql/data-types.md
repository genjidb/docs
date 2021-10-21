---
title: "Data Types"
date: 2020-03-29T17:44:36+04:00
weight: 20
description: >
  This chapter describes the list of data types
---

Genji provides a list of simple data types to store and manipulate data.

| Name     | Description                                                                              | From                     | To                      |
| -------- | ---------------------------------------------------------------------------------------- | ------------------------ | ----------------------- |
| BOOL     | Can be either `true` or `false`                                                          | `false`                  | `true`                  |
| INTEGER  | Signed integer which takes 1, 2, 4 or 8 bytes depending on the size of the stored number | -9223372036854775808     | 9223372036854775807     |
| DOUBLE   | 8 bytes decimal                                                                          | -1.7976931348623157e+308 | 1.7976931348623157e+308 |
| BLOB     | Variable size BLOB of data                                                               |                          |                         |
| TEXT     | Variable size UTF-8 encoded string                                                       |                          |                         |
| ARRAY    | ARRAY of values of any type                                                              |                          |                         |
| DOCUMENT | Object that contains pairs that associate a string field to a value of any type          |                          |                         |

## The case of NULL

In Genji, _NULL_ is treated as both a value and a type. It represents the absence of data, and is returned in various cases:

- when selecting a field that doesn't exists
- when selecting a field whose value is NULL
- as the result of the evaluation of an expression

## Conversion

Whenever Genji needs to manipulate data of different types, depending on the situation it will rely on either:

- **explicit conversion**: The source type and destination type are clearly identified. Ex: When inserting data to field with a constraint or when doing a `CAST`.
- **implicit conversion**: Two values of different types need to be compared or used by an operator during the evaluation of an [expression]({{< relref "/docs/genji-sql/expressions.md" >}})

### Explicit conversion

Explicit conversion is used when we want to convert a value of a _source_ type into a _target_ type.
However, Genji types are not all compatible with one another, and when a user tries to convert them, Genji returns an error.
Here is a table describing type compatibility.

| Source type | Target type | Converted                                         | Example                                    |
| ----------- | ----------- | ------------------------------------------------- | ------------------------------------------ |
| BOOL        | INTEGER     | yes, `1` if `true`, otherwise `0`                 | `CAST(true AS INTEGER) -> 1`               |
| BOOL        | TEXT        | yes, `'true'` if `true`, otherwise `'false'`      | `CAST(true AS TEXT) -> 'true'`             |
| INTEGER     | BOOL        | yes, `false` if zero, otherwise `true`            | `CAST(10 AS BOOL) -> true`                 |
| INTEGER     | DOUBLE      | yes                                               | `CAST(10 AS DOUBLE) -> 10.0`               |
| INTEGER     | TEXT        | yes                                               | `CAST(10 AS TEXT) -> '10'`                 |
| DOUBLE      | INTEGER     | yes, cuts off the decimal part                    | `CAST(10.5 AS DOUBLE) -> 10`               |
| DOUBLE      | TEXT        | yes                                               | `CAST(10.5 AS DOUBLE) -> '10.5'`           |
| TEXT        | BOOL        | yes, if the content is a valid boolean            | `CAST('true' AS BOOL) -> true`             |
| TEXT        | INTEGER     | yes, if the content is a valid integer            | `CAST('10' AS INTEGER) -> 10`              |
| TEXT        | DOUBLE      | yes, if the content is a valid decimal number     | `CAST('10.4' AS DOUBLE) -> 10.4`           |
| TEXT        | BLOB        | yes, if the content is a valid base64 value       | `CAST('aGVsbG8K' AS BLOB) -> 'aGVsbG8K'`   |
| TEXT        | ARRAY       | yes, if the content is a valid json array         | `CAST('[1, 2, 3]' AS ARRAY) -> [1, 2, 3]`  |
| TEXT        | DOCUMENT    | yes, if the content is a valid json object        | `CAST('{"a": 1}' AS DOCUMENT) -> {"a": 1}` |
| BLOB        | TEXT        | yes, the content will be encoded in base64        |                                            |
| ARRAY       | TEXT        | yes, the content will be encoded as a json array  | `CAST([1, 2, 3] AS TEXT) -> '[1, 2, 3]'`   |
| DOCUMENT    | TEXT        | yes, the content will be encoded as a json object | `CAST({a: 1} AS DOUBLE) -> '{"a": 1}'`     |
| NULL        | any type    | yes, `NULL`                                       | `CAST(NULL AS DOUBLE) -> NULL`             |

### Implicit conversion

There is only one kind of implicit conversion: `INTEGER` to `DOUBLE`. This usually takes place during the evaluation of an [expression]({{< relref "/docs/genji-sql/expressions" >}}) involving INTEGER and DOUBLE values.
No other conversion is applied unless it's explicit.
