---
title: "What is Genji?"
linkTitle: "Documentation"
weight: 20
type: list
menu:
  main:
    weight: 20
---

Genji is a document-oriented, embedded, SQL database written in Go.

It combines the power of **SQL** with the versatility of **documents** to provide a maximum of flexibility with no compromise.

Here is a list of Genji's main features:

- **SQL and documents**: Use a powerful SQL language designed for documents as first-class citizen.
- **Flexible schemas**: Define your table with strict schemas, partial schemas, or no schemas at all.
- **Transaction support**: Fully serializable transactions with multiple readers and single writer. Readers don't block writers and writers don't block readers.

## Concepts

Genji's main concepts are not new and semantics have been chosen to match as much as possible what is already existing in other databases:

| Classic SQL databases | Genji    |
| --------------------- | -------- |
| Table                 | Table    |
| Schema                | Schema   |
| Row                   | Document |
| Column                | Field    |

- **Table**: A collection of documents
- **Schema**: A list of constraints that apply on all or certain fields of every documents of the table.
- **Document**: A list of fields
- **Field**: A key-value pair
