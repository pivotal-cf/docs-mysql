---
title: Cluster Configuration
owner: MySQL
---


This page documents the various configuration decisions that have been made in relation to MariaDB and Galera in cf-mysql-release.

### SST method

Galera supports multiple methods for [State Snapshot Transfer](http://www.percona.com/doc/percona-xtradb-cluster/5.5/manual/state_snapshot_transfer.html).
The `rsync` method is usually fastest. The `xtrabackup` method has the advantage of keeping the donor node writeable during SST. We have chosen to use `xtrabackup`.

### InnoDB Log Files
Our cluster defaults to 1GB for log file size to support larger blob.

### Max User Connections
To ensure all users get fair access to system resources, we default each user's number of connections to 40. Operators can override this setting, per plan, in the Service Plans configuration pane.

### Skip External Locking
Since each Virtual Machine only has one mysqld process running, we do not need external locking.

### Max Allowed Packet
We allow blobs up to 256MB. This size is unlikely to limit a user's query, but is also manageable for our InnoDB log file size. Consider using our Riak CS service for storing large files.

### Innodb File Per Table
Innodb allows using either a single file to represent all data, or a separate file for each table. We chose to use a separate file for each table as this provides more flexibility and optimization. For a full list of pros and cons, see MySQL's documentation for [InnoDB File-Per-Table Mode](http://dev.mysql.com/doc/refman/5.5/en/innodb-multiple-tablespaces.html).

### Innodb File Format
To take advantage of all the extra features available with the `innodb_file_per_table = ON` option, we use the `Barracuda` file format.

### Temporary Tables
MySQL is configured to convert temporary in-memory tables to temporary on-disk tables when a query EITHER generates more than 16 million rows of output or uses more than 32MB of data space.
Users can see if a query is using a temporary table by using the EXPLAIN command and looking for "Using temporary," in the output.
If the server processes very large queries that use /tmp space simultaneously, it is possible for queries to receive no space left errors.

### Reverse Name resolution
Since our connection authentication is implemented using user credentials, the default implementation does not restrict to host names from which a user may connect. To save time on each new connection, we've turned off reverse name resolution by default.

### Large Data File Ingestion
Now that a major bug has been fixed in MariaDB, we enable `wsrep_load_data_splitting` which splits large data imports into separate transactions to enable loading data into a MariaDB cluster.
