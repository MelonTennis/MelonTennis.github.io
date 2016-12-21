---
layout: post
title: Using Database with Python Week2
subtitle: Relational Databases and SQLite 
date: 2016-12-20
categories: Coursera
tags: [Database]
description: Week2, Relational Databases and SQLite
catalog: true
---

### Relational Databases and SQLite

Definition:

> Database - contains many tables
>
> • Relation (or table) - contains tuples and attributes
>
> • Tuple (or row) - a set of fields that generally represents an “object” like a person or a music track
>
> • Attribute (also column or field) - one of possibly many elements of data corresponding to the object represented by the row

**Relation(Table)**:

|            | Attribute         |       |
| :--------- | ----------------- | :---- |
| Tuple(Row) | Tuple/Attribute   | Tuple |
|            | Attribute(column) |       |

#### SQL

> Structured Query Language is the language we use to issue commands to the database
>
> Creat a table/ Retrive data/ Insert / Delete

> Using [SQLite Browser](http://sqlitebrowser.org/)

Some Examples:

Creat/ Insert/ Delete/ Retrive/ Sort/ Count selected numbers

```sql
CREATE TABLE Users(
	name VARCHAR(128),
  	email VARCHAR(128)
)
```

```sql
INSERT INTO Usesrs(name, email)VALUES('name','email@sth.com')
```

```sql
DELETE FROM Users WHERE email = 'email@sth.com'
```

```sql
UPDATE USers SET name = 'newname' WHERE email = 'email@sth.com'
```

```sql
SELECT*FROM Users WHERE email = 'email@sth.com'
```

```sql
SELECT*FROM Users ORDER BY name(email)
```

```sql
SELECT COUNT(*) FROM Users
```

小assessment： 找到一堆文件里的email域名并统计。~~域名是什么玩意？~~死蠢的试验了一下： 

```python
org = re.search("@[\w.]+", email).group().strip().split('@')[-1]
```

代码主体部分还是提供好了的：

```python
import sqlite3
import re

conn = sqlite3.connect('emaildb.sqlite')
cur = conn.cursor()

cur.execute('''
DROP TABLE IF EXISTS Counts''')

cur.execute('''
CREATE TABLE Counts (org TEXT, count INTEGER)''')

fname = raw_input('Enter file name: ')
if ( len(fname) < 1 ) : fname = 'mbox.txt'
fh = open(fname)
for line in fh:
    if not line.startswith('From: ') : continue
    pieces = line.split()
    email = pieces[1]
    org = re.search("@[\w.]+", email).group().strip().split('@')[-1]
    print org
    cur.execute('SELECT count FROM Counts WHERE org = ? ', (org, ))
    row = cur.fetchone()
    if row is None:
        cur.execute('''INSERT INTO Counts (org, count)
                VALUES ( ?, 1 )''', ( org, ) )
    else :
        cur.execute('UPDATE Counts SET count=count+1 WHERE org = ?',
            (org, ))
    # This statement commits outstanding changes to disk each
    # time through the loop - the program can be made faster
    # by moving the commit so it runs only after the loop completes
    conn.commit()

# https://www.sqlite.org/lang_select.html
sqlstr = 'SELECT org, count FROM Counts ORDER BY count DESC LIMIT 10'

print
print "Counts:"
for row in cur.execute(sqlstr) :
    print str(row[0]), row[1]

cur.close()

```

~~洗洗睡~~

