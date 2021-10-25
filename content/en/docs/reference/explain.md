---
title: "EXPLAIN"
date: 2021-10-21T14:00:44+04:00
description: >
  Generate query plan
---

## Synopsis

### EXPLAIN statement

```js {.rr}
Diagram(
  Sequence(
    "EXPLAIN",
    Choice(
      0,
      Link("select-stmt", "../select#select-stmt"),
      Link("insert-stmt", "../insert#insert-stmt"),
      Link("update-stmt", "../update#update-stmt"),
      Link("delete-stmt", "../delete#delete-stmt")
    )
  )
);
```
