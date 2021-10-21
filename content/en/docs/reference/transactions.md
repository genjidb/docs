---
title: "Transactions"
date: 2021-10-22T14:00:44+04:00
description: >
  Manage transactions
---

## Synopsis

### begin-transaction-stmt

```railroad
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

### rollback-transaction-stmt

```railroad
Diagram(Sequence("ROLLBACK", Optional("TRANSACTION", "skip")));
```

### commit-transaction-stmt

```railroad
Diagram(Sequence("COMMIT", Optional("TRANSACTION", "skip")));
```
