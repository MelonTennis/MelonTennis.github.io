---
layout: post
title: Using Database with Python Week3
subtitle: Datebase design
date: 2016-12-21
categories: Coursera
tags: [Python, Database]
description: Week3, Database design
catalog: true
---

### Database Design

Building a Data Model:

> Basic Rule: Don’t put the same string data in twice - use a relationship instead

Database Normalization:

> Do not replicate data - reference data - point at data
>
> Use integers for keys and for references
>
> Add a special “key” column to each table which we will make references to. By convention, many programmers call this column “id”

Three kinds of Keys:

> * Primary key - generally an integer autoincrement field-> Never use your logical key as the primary key.
>
>
> * Logical key - What the outside world uses for lookup
> * Foreign key - generally an integer key pointing to a row in another table -> when a table has column that contains a key which points to the primary key of another table.

Relationship Building:

> less scan and less storage, remove the replicated data and replace it with reference  -> linked by ***foreign keys***

**JOIN** Operation:

> links across several tables as part of a select operation

One Example:

```sql
select Album.title, Artist.name from Album join Artist on Album.artist_id = Artist.id
```

> Without on:  all possible combinations of rows.

Another Example:

```sql
select Track.title, Artist.name, Album.title, Genre.name from Track join Genre join Album join Artist on Track.genre_id = Genre.id and Track.album_id = Album.id and Album.artist_id = Artist.id
```

Select: what we want

From: tables with data

On: joint conditions

> By normalizing the data and linking it with integer keys, the overall amount of data which the relational database must scan is far lower than if the data were simply flattened out
>
> tradeoff - spend some time designing your database so it continues to be fast

#### Additional Topics

> Indexes improve access performance for things like string fields
>
> Constraints on data - (cannot be NULL, etc..)
>
> Transactions - allow SQL operations to be grouped and done as a unit

[umich slides](https://www.dr-chuck.net/pythonlearn/slides/EN_us/Py4Inf-14-Databases.pdf)

Assessments 还是很简单基本随便写写。。。