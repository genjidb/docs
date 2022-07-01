---
title: "Table Basics"
date: 2020-03-30T20:27:04+04:00
weight: 10
description: >
  How to create and remove tables
---

Though Genji stores its data in tables, there is no concept of rows or columns. In Genji:

- a **table** is a collection of _documents_
- a **document** is a collection of _fields_
- a **field** is a key-value pair

Each document is assigned a primary key, which is a unique identifier.

The order in which documents are returned when reading a table is not guaranteed unless sorted explicitly.

## Schemas

Like relational databases, tables can have a schema to control and validate data. There are three kinds of schema that can be defined in Genji:

- _strict_ schemas
- _partial_ schemas
- _no_ schema (or schemaless)

### Strict schemas

This kind of schema is similar to the ones found in other databases: It defines _all_ the fields of every document.

But unlike other databases, it has some really interesting properties that are taylored for documents:

- It is possible to define constraints on nested fields
- When inserting a document that contains fields that where not defined in the schema, these fields are ignored

Let's see an example:

```sql
CREATE TABLE users (
    id        INTEGER PRIMARY KEY,
    name      TEXT NOT NULL,
    age       INTEGER,
    email     TEXT NOT NULL UNIQUE
    address (
      zipCode TEXT
    ),

    CHECK(age >= 0)
)
```

This will create a table `users` with the following constraints:

- All documents must have a non-empty `id` field, whose type can be converted to an integer. This field will be used as the primary key of the table and will be stored as an integer.
- All documents must have a non-empty `name` field that can be converted to `TEXT`.
- If a document has an `age` field, it will be converted to an integer. The field has to be positive.
- All documents must have a non-empty email field that can be converted to `TEXT` and it must be unique across the whole table.
- If a document has an `address` field and its value is a document with a `zipCode` field, then its value will be converted to `TEXT`.

Inserting documents on strict tables can be done in multiple ways:

```sql
INSERT INTO users
  (id, name, age, email, address)
  VALUES (
    1,
    'Jim',
    10,
    'jim@host.com',
    {
      zipCode: '12345'
    }
  )

-- or

INSERT INTO users VALUES {
  id: 1,
  name: 'Jim',
  age: 10,
  email: 'jim@host.com',
  address: {
    zipCode: '12345'
  }
}
```

### Partial schemas

When dealing with data coming from different sources (APIs, files, streams, etc.), is might be useful to define constraints only on a set of fields, while storing the other fields as-is.
These kind of schemas are called _partial schemas_.

Let's create a table that can be used to store some Genji's Github issues consumed from the Github API:

```sql
CREATE TABLE github_issues (
  id            INTEGER PRIMARY KEY,
  title         TEXT,
  user (
    id          INTEGER UNIQUE
    site_admin  BOOLEAN,

    ...
  ),

  ...
)
```

By using the `...` notation, it is possible to tell Genji to insert _all_ fields of incoming documents but to apply constraints only to certain fields. It also works on nested documents.

Here is how to use the Genji CLI to populate this table:

```bash
curl https://api.github.com/repos/genjidb/genji/issues | genji insert --db mydb -t github_issues
```

{{% alert title="Tip" %}} Partial schemas are also interesting when you want to create indexes or specific constraints on certain fields{{% /alert %}}

### Schemaless tables

Unlike relational databases, it is also possible to define schemaless tables.
Documents stored in schemaless tables can be completely different from one another.

```sql
CREATE TABLE teams;

-- equivalent to

CREATE TABLE teams (...);
```

Inserting documents can then be done with no constraints:

```sql
INSERT INTO teams (name, league) VALUES ('Real Madrid', 'Ligua');
INSERT INTO teams (name, league, members) VALUES ('PSG', 'Ligue 1', ['Messi']);
```

## Dropping a table

`CREATE TABLE` will return an error if the table already exists.

To remove a table and all of its content, use the `DROP TABLE` command:

```sql
DROP TABLE users;
```

This will remove the `users` table and all of its documents. If `DROP TABLE` is called on a non-existing table, it will return an error.
