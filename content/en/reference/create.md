---
title: "CREATE TABLE"
date: 2021-10-21T14:00:44+04:00
description: >
  Define a new table
---

## Synopsis

```sql
CREATE TABLE [ IF NOT EXISTS ] table_name
  [optional_schema]

optional_schema:
  (
        [ field_constraint [ , ... ] ]
      | [ table constraint [ , ... ] ]
  )
```

```mermaid
flowchart TD
  subgraph one
    direction LR
    create(CREATE)
      --> table(TABLE)
      --> if("IF") --> not("NOT") --> exists("EXISTS");
    table --> tableName(table-name);
    exists --> tableName;
  end;

  subgraph two
    direction LR
    lparen("(") --> rparen(")");
  end;

  one --> two;
```

```mermaid
flowchart LR
  create(CREATE)
    --> table(TABLE)
    --> if("IF") --> not("NOT") --> exists("EXISTS");
  table --> tableName(table-name);
  exists --> tableName;

  tableName --> schema("optional-schema");
```

optional-schema:

```mermaid
flowchart LR
  lparen("(")
  rparen(")")
  fieldDefComma(",")
  tableConstraintComma(",")
  lparen --> fieldDefs("field-definitions")
  lparen --> tableConstraint("table-constraint")
  fieldDef --> fieldDefComma --> fieldDef
  fieldDef --> rparen
  tableConstraint ---> rparen
  tableConstraint --> tableConstraintComma --> tableConstraint
  fieldDef --> tableConstraint
```

```pikchr
box color red wid 2.6in \
    "Click on any diagram on this page" big \
    "to see the Pikchr source text" big
```
