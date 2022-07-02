---
title: "SQL Introduction"
date: 2020-03-30T20:27:04+04:00
weight: 10
description: >
  Introduction to Genji SQL
---

## Tables

Though Genji stores its data in tables, there is no concept of rows or columns. In Genji:

- a **table** is a collection of _documents_
- a **document** is a collection of _fields_
- a **field** is a key-value pair

Each document is assigned a primary key, which is a unique identifier.

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

{{% alert title="Tip" %}}Partial schemas are also interesting when you want to create indexes or specific constraints on certain fields{{% /alert %}}

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

{{% alert title="Warning" color="warning" %}}When using partial schemas or schemaless tables, any undeclared numeric field is considered a floating point decimal number.
{{% /alert %}}

## Dropping a table

`CREATE TABLE` will return an error if the table already exists.

To remove a table and all of its content, use the `DROP TABLE` command:

```sql
DROP TABLE users;
```

This will remove the `users` table and all of its documents. If `DROP TABLE` is called on a non-existing table, it will return an error.

## Inserting documents

The `INSERT` statement is used to add documents to tables. It was designed with two goals in mind:

- provide an API familiar to users used to other SQL databases
- provide an alternative API designed to simplify inserting complex documents

Here is the first form:

```sql
INSERT INTO countries (name, population) VALUES ('France', 67900000);

-- if the countries table has a strict or partial schema, field names can be omitted.
INSERT INTO countries VALUES ('France', 67900000);
```

Because Genji is a document database, this form also supports nested fields and arrays:

```sql
INSERT INTO bands (name, members, albums)
  VALUES (
    "Guns N' Roses",
    ["Axl Rose", "Slash", "Steven Adler", "Duff McKagan"],
    [
      {
      name: "Appetite for Destruction",
      releaseYear: "1987"
      },
      {
        name: "G N' R Lies",
        releaseYear: "1988"
      }
    ]
  );
```

The second form uses the [document literal]({{< relref "/docs/essentials/expressions" >}}#document-literal) notation. It is useful when inserting complex documents that would make the first form too verbose:

```sql
INSERT INTO bands VALUES {
  "name": "Guns N' Roses",
  "members": ["Axl Rose", "Slash", "Steven Adler", "Duff McKagan"],
  "albums": [
      {
        name: "Appetite for Destruction",
        releaseYear: "1987"
      },
      {
        name: "G N' R Lies",
        releaseYear: "1988"
      }
  ]
}
```

{{% alert title="Tip" %}}JSON documents can be used in the insert statement directly{{% /alert %}}

## Selecting documents

Querying documents from a table can be achieved by using the `SELECT` statement.
The output of `SELECT` statements is a strean of documents that can be decoded and represented in any form.

Here is an example of selecting all documents of the `users` table:

```sql
SELECT * FROM players;
```

Here is a json representation of the output

```json
{
    "name": "Rafael Nadal",
    "age": 36,
    "nationality": "Spain",
    "career": {
      "australia": 2,
      "france": 14,
      "wimbledon": 2,
      "us": 4
    },
    "coach": [
      "Francisco Roig",
      "Carlos Moyá",
      "Marc López"
    ]
}
{
    "name": "Roger Federer",
    "age": 40,
    "nationality": "Switzerland",
    "career": {
      "australia": 6,
      "france": 1,
      "wimbledon": 8,
      "us": 5
    },
    "coach": [
      "Ivan Ljubičić",
      "Severin Lüthi",
    ]
}
{
    "name": "Andrew Barron Murray",
    "coach": [
      "Ivan Lendl"
    ]
}
```

Let's break it down:

- `SELECT`: Run the SELECT command
- `*`: This is the _projection_, it indicates how to build the documents returned by the result of the query. Here, we are using a special projection called the _wildcard_, which is a way to tell Genji to simply return all of the fields of each document.
- `FROM players`: Indicates from which table we want to query the data.

## Understanding projections

Now, let's query only the name and age of each player:

```sql
SELECT name, age FROM players;
```

```json
{
  "name": "Rafael Nadal",
  "age": 36
}
{
  "name": "Roger Federer",
  "age": 40
}
{
  "name": "Andrew Barron Murray",
  "age": null
}
```

The result contains three documents, all of them have a `name` and `age` fields.

A projection guarantees that all the documents returned by the query will contain the selected fields, even if the original documents don't have that information. In our example, the `Andrew Barron Murray` document doesn't have an `age` field, so its projected value is `null`.
The only exception is for the `*` wildcard, which projects all the fields of the original document.

## Querying nested fields

Let's determine how many French Open each of them has won in their career:

```sql
SELECT name, career.france FROM players;
```

```json
{
  "name": "Rafael Nadal",
  "career.france": 14
}
{
  "name": "Roger Federer",
  "career.france": 1
}
{
  "name": "Andrew Barron Murray",
  "career.france": null
}
```

In this example, we used a [path]({{< relref "/docs/essentials/documents" >}}#paths) to select the `career.france` field.

Let's add the information about the first coach of each player:

```sql
SELECT name, career.france, coach[0] FROM players;
```

```json
{
  "name": "Rafael Nadal",
  "career.france": 14,
  "coach[0]": "Francisco Roig"
}
{
  "name": "Roger Federer",
  "career.france": 1,
  "coach[0]": "Ivan Ljubičić"
}
{
  "name": "Andrew Barron Murray",
  "career.france": null,
  "coach[0]": "Ivan Lendl"
}
```

`coach[0]` is a notation that indicates to select the element at index `0` of the `coach` array.

## Filter documents

Until now, we always performed our queries on every document of the table.
Let's only query those whose `career` document is set.

```sql
SELECT name FROM players WHERE career IS NOT NULL;
```

```json
{
  "name": "Rafael Nadal"
}
{
  "name": "Roger Federer"
}
```

This time, the result contains only two documents.

The `WHERE` clause allows filtering the documents returned. To do that, it evaluates an [expression]({{< relref "/docs/essentials/expressions" >}}) on every document:

- if the result of the evaluation is _truthy_, the document is selected
- if the result of the evaluation is _falsy_, the document is filtered out

```sql
SELECT name, age FROM players WHERE age < 40;
```

```json
{
  "name": "Rafael Nadal",
  "age": 36
}
```

In this example, only Rafael Nadal satisfies the query:

- Roger Federer's age is 40 which is not `< 40`
- Andrew Barron Murray's age is `null`, which is also not `< 40`

## Filtering on values in nested objects

We can filter on values in nested arrays using the `IN` operator:

```sql
SELECT name, coach FROM players WHERE 'Ivan Ljubičić' IN coach;
```

```json
{
  "name": "Roger Federer",
  "coach": ["Ivan Ljubičić", "Severin Lüthi"]
}
```

And values in nested documents:

```sql
SELECT name, career.wimbledon AS wimbledon FROM players WHERE career.wimbledon > 3;
```

```json
{
  "name": "Roger Federer",
  "wimbledon": 8
}
```

## Ordering results

The order in which documents are returned when reading a table is not guaranteed unless sorted explicitly.

To control the order of the results, we need to use the `ORDER BY` clause

```sql
SELECT name, career.australia AS australia FROM players ORDER BY career.australia;
```

```json
{
  "name": "Andrew Barron Murray",
  "australia": null
}
{
  "name": "Rafael Nadal",
  "australia": 2
}
{
  "name": "Roger Federer",
  "australia": 6
}
```

The order in which documents appear depends on three factors:

- the _direction_ of the order
- the _type_ of the field used for ordering
- the _value_ of the field used for ordering

By default, the direction is ascending, from the smallest value to the highest.

When it comes to ordering, there is a hierarchy between types:

`NULL` < `BOOLEAN` < numbers < `TEXT` or `BLOB`

In the example above, the `career` field of Andrew Barron Murray doesn't exist, so it is treated as `null`, and then appears first in the result.

Then, Rafael Nadal and Roger Federer have an `career.australia` field which is an `INTEGER`, there are compared with each other and returned in ascending order.

The direction can be controlled by using `ASC` or `DESC` clauses.

```sql
SELECT name, career.australia AS australia FROM players ORDER BY career.australia ASC;
```

```json
// returns the same results as above
```

```sql
SELECT name, career.australia AS australia FROM players ORDER BY career.australia DESC;
```

```json
{
  "name": "Roger Federer",
  "australia": 6
}
{
  "name": "Rafael Nadal",
  "australia": 2
}
{
  "name": "Andrew Barron Murray",
  "australia": null
}
```

## Using functions

Projections can also use functions to add more power to the queries.

### Select the primary key

Every document has a primary key, which is a unique value.
When a document is inserted without an explicit primary key, an implicit `docid` is created automatically. Implicit primary keys don't appear in the results though, even when using `SELECT *`.
To select them, we can use the `pk()` function.

```sql
SELECT pk(), name FROM players;
```

```json
{
  "pk()": [
    1
  ],
  "name": "Rafael Nadal"
}
{
  "pk()": [
    2
  ],
  "name": "Roger Federer"
}
{
  "pk()": [
    3
  ],
  "name": "Andrew Barron Murray"
}
```

Because the primary key can be composite, `pk()` always returns an array.

Here is an example with another table with a composite primary key

```sql
CREATE TABLE cars (
  brand TEXT,
  name TEXT,
  year INTEGER,

  PRIMARY KEY (brand, name)
);

INSERT INTO cars VALUES ('Ford', 'Mustang', 1965);
INSERT INTO cars VALUES ('Peugeot', '205', 1984);

SELECT pk(), name FROM cars;
```

```json
{
  "pk()": [
    "Ford",
    "Mustang"
  ],
  "name": "Mustang"
}
{
  "pk()": [
    "Peugeot",
    "205"
  ],
  "name": "205"
}
```

### Other SELECT clauses

The SELECT statement is very rich and can do much more. Read the [SELECT reference]({{< relref "/docs/reference/select" >}}) for more information.

## Updating documents

The `UPDATE` statement makes it possible to update one or more documents in a table.

Consider a table `users` with the following documents in it.

```json
{
    "name": "Thor",
    "age": 1000
}
{
    "name": "Hulk",
    "group": "Avengers",
    "age": 42
}
```

```sql
UPDATE users SET group = "Avengers"
```

Let's break it down:

- `UPDATE users` runs the `UPDATE` statement on the `users` table
- `SET` indicates the list of changes we want to perform
- `group = "Avengers"` sets the `group` field of the document to the value "Avengers"

Without a `WHERE` clause, this statement will run on all the documents of the table. Here is the state of the table after running this command:

```json
{
    "name": "Thor",
    "age": 1000,
    "group": "Avengers"
}
{
    "name": "Hulk",
    "age": 42,
    "group": "Avengers"
}
```

The first document didn't have a `group` field before. It's because the `SET` clause actually sets fields in the document, regardless of their existence. This is a good way to add new fields to documents.

Since we can add or modify fields using the `SET` clause, it is also possible to delete fields using the `UNSET` clause:

```sql
UPDATE users UNSET age;
```

This will delete the `age` field from all the documents. If the field doesn't exist it does nothing.

To update only a subset of documents, we can use the `WHERE` clause. In the following example, only the documents that satisfy the `age = 2` condition will be updated.

```sql
UPDATE users UNSET group WHERE age = 2;
```

## Deleting documents

Documents can be deleted using the `DELETE` statement.

Let's start with the simplest form:

```sql
DELETE FROM products;
```

This command deletes all the documents of the `products` table.

To delete only a few documents, use the `WHERE` clause:

```sql
DELETE FROM products WHERE sales_count < 1000;
```

For every document, the `WHERE` clause evaluates any [expression]({{< relref "/docs/essentials/expressions" >}}) that follows, here `sales_count < 1000`. If the result is truthy, the document gets deleted.

The `DELETE` statement doesn't return an error if no document matches the `WHERE` clause, or if there aren't any document in the table.
