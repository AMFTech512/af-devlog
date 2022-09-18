---
title: Primary keys in Cassandra
date: 2022-08-20T15:11:07Z
---

Primary keys in Cassandra
---

Here's a good article that I read about this: https://www.baeldung.com/cassandra-keys

Basically the primary key is made up of two parts, the partition key and the clustering columns. The partition key is hashed to determine which node (partition) the row should be stored on. The clustering columns are used to sort the rows inside of each partition. 