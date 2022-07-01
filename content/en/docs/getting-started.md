---
title: "Getting started"
linkTitle: "Getting started"
date: 2020-03-29T17:22:52+04:00
weight: 20
description: >
  How to install and use Genji
---

## Prerequisites

Genji requires at least Go 1.18.

## Installation

To install the Genji database, run this command:

```bash
go get github.com/genjidb/genji
```

## Golang API documentation

To learn how to embed Genji in your Go code, follow the instructions in the [Go package documentation](https://pkg.go.dev/github.com/genjidb/genji?tab=doc).

## Try it out!

To try Genji without writing code, you can use the Genji command-line shell.

First, install it:

```bash
go get github.com/genjidb/genji/cmd/genji
```

To open an in-memory database, simply type:

```bash
genji
```

You can then enter your [SQL queries]({{< relref "/docs/essentials/_index.md" >}}) directly in the shell.

It is also possible to create an on-disk database by specifying a directory:

```bash
genji mydb
```

## Next step

Once Genji is setup, follow the [Genji SQL]({{< relref "/docs/essentials/_index.md" >}}) chapter to learn how to run queries.
