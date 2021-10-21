---
title: "EXPLAIN"
date: 2021-10-21T14:00:44+04:00
description: >
  Generate query plan
---

## Synopsis

### explain-stmt

```railroad
Diagram(
  Sequence(
    "EXPLAIN",
    Choice(0,
      {{% rrlink "select-stmt" "select#select-stmt" %}},
      {{% rrlink "insert-stmt" "insert#insert-stmt" %}},
      {{% rrlink "update-stmt" "update#update-stmt" %}},
      {{% rrlink "delete-stmt" "delete#delete-stmt" %}}
    )
  )
);
```
