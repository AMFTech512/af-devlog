---
title: Basic CQL Commands
date: 2022-08-15T14:19:58Z
---

Basic CQL Commands
---

### A few notes about syntax
- If the name of keyspace, table, MV, etc has a capital letter, you need to wrap it in double quotes. E.g. `select * usersById;` will fail, but `select * from "usersById";` won't.
- String values go in single quotes (not double quotes). E.g. your query would look like `select * from users where email='austin@austinfay.com';`.
- UUIDs don't need to be wrapped. E.g. `select * from users where id=07cc93f0-1ca6-11ed-8f96-4f0170477b3f;`.
- Lists are denoted by `()`. E.g. `select * from users where id in (25bf8250-1ca6-11ed-8f96-4f0170477b3f, 2778a400-1ca6-11ed-8f96-4f0170477b3f);`.
- Always end a query with a semicolon.

## Commands

Here are some basic CQL commands that you will frequently, so it might be a good idea to memorize the syntax. This page will be focused on syntax. I'll save the details of choosing primary keys, replication, etc for other pages.

### Create a keyspace

```sql
CREATE KEYSPACE killrvideo
WITH replication = {
'class':'SimpleStrategy',
'replication_factor': 1
};
```

### Set current keyspace

This is so that you don't have to specify `keyspace.table_name` every time, just `table_name`.
```sql
USE killrvideo;
```

### Create a table

```sql
CREATE TABLE videos_by_tag (
tag TEXT,
video_id UUID,
added_date TIMESTAMP,
title TEXT,
PRIMARY KEY ((tag), video_id)
);
```

In this case, `tag` is the partition column, and `video_id` is a clustering column.

### Get metadata for table

```sql
DESCRIBE TABLE videos_by_tag;
```

### Insert row into table

```sql
INSERT INTO videos_by_tag (video_id, added_date, title, tag)
VALUES (245e8024-14bd-11e5-9743-8238356b7e32, '2012-04-03', 'Cassandra & SSDs', 'cassandra');
```

### Import data into table from CSV

The format of the csv is pretty simple; the first row is just the column names and the subsequent rows are the values. As far as I can tell, double quotes are optional for values unless your values contain commas.

```sql
COPY videos_by_tag(tag, video_id, added_date, title)
FROM '<PATH>/videos-by-tag.csv'
WITH HEADER = TRUE;
```

### Select query

```sql
SELECT *
FROM videos_by_tag
WHERE tag = 'cassandra';
```

Or to select specific columns:
```sql
SELECT video_id, added_date
FROM videos_by_tag
WHERE tag = 'cassandra';
```

### Delete all data from a table (without deleting the table itself)

```sql
TRUNCATE videos;
```