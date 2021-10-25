---
title: "Transactions"
date: 2021-10-22T14:00:44+04:00
description: >
  Manage transactions
---

## Synopsis

### BEGIN TRANSACTION statement

```js {.rr}
Diagram(
  Sequence(
    "BEGIN",
    Optional("TRANSACTION", "skip"),
    Optional(
      Choice(0, Sequence("READ", "ONLY"), Sequence("READ", "WRITE")),
      "skip"
    )
  )
);
```

### ROLLBACK TRANSACTION statement

```js {.rr}
Diagram(Sequence("ROLLBACK", Optional("TRANSACTION", "skip")));
```

### COMMIT TRANSACTION statement

```js {.rr}
Diagram(Sequence("COMMIT", Optional("TRANSACTION", "skip")));
```
