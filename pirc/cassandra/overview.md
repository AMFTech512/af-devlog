---
title: Cassandra overview
date: 2022-08-15T14:07:07Z
---

Cassandra overview
---

Cassandra is a database that has one primary goal: to scale horizontally with cheap hardware. They envision that you can run your db with lots of raspberry pis rather than one huge decked out machine. There are a ton of under-the-hood things that Casandra does to achieve this goal, but the primary one has to do with how it figures out where to store table rows.

Cassandra is meant to be deployed to multiple comuters (nodes), and it sets them up as a ring like so: ![](https://www.researchgate.net/profile/Waheed-Iqbal/publication/321768377/figure/fig2/AS:571067251265537@1513164450407/Apache-Cassandra-Ring.png). Cassandra creates a hash table of sorts where each node represents a slot in the hash table (kind of). It's actually a slightly more sophisticated mechanism called "consistent hashing" (which you can look up on Google). A table has a primary key that is comprised of one or more partition columns and clustering columns. The partition columns are hashed to determine which node (or "partition") to store the row in. This makes it so that Cassandra can efficiently find rows based on a query where the filters are based on the columns in the primary key. 

However, sometimes it's necessary to query based on columns that are not in the primary key. In this case, you create a *materialized view*. A materialzed view is a copy of the original table that is managed automatically by Cassandra, but has a different primary key, allowing you to do queries based on different columns. 

This all follows the philosophy that Cassandra has adopted that tables and data structures should be designed around queries.