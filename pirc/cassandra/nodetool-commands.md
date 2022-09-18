---
title: nodetool commands for Cassandra
date: 2022-08-20T16:27:06Z
---

nodetool commands
---

### nodetool status

The status command shows information about the entire cluster, particularly the state of each
node, and information about each of those nodes: IP address, data load, number of tokens,total percentage of data saved on each node, host ID, and datacenter and rack. We will discuss
these in detail as the course progresses.

### nodetool describecluster

describecluster shows the settings that are common across all of the nodes in the cluster
and the current schema version used by each node. We have a simple one node cluster
currently but will add to it soon.

### nodetool drain

The drain command stops writes from occurring on the node and flushes all data to disk.
Typically, this command may be run before stopping an Apache CassandraTM node.

### nodetool stopdaemon

The stopdaemon command stops a node's execution. Wait for it to complete.

### nodetool flush

The flush command commits all written (memtable) data to disk. Unlike
drain, flush allows further writes to occur.

