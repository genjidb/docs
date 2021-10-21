---
title: "REINDEX"
date: 2021-10-21T14:00:44+04:00
description: >
  Reindex tables, indexes or everything
---

## Synopsis

### reindex-stmt

```railroad
Diagram(
  Sequence("REINDEX", Optional(Choice(0, "table-name", "index-name"), "skip"))
);
```
