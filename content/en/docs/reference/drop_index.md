---
title: "DROP INDEX"
date: 2021-10-21T14:00:44+04:00
description: >
  Delete an index and all of its content
---

## Synopsis

### drop-index-stmt

```railroad
Diagram(
  Sequence("DROP", "INDEX", Optional(Sequence("IF", "EXISTS"), "skip"), "index-name")
);
```
