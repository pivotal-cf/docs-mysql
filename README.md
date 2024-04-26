docs-mysql
==========

Docs for VMware SQL with MySQL for Tanzu Application Service

## Which branch to use?

**Note**: Provide instructions in your PRs to indicate which branches you want Docs to apply your commits to.

| Branch name  | Use for...                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------|
| master       | Not used                                                                                                                         |
| 3.1          | 3.1                                                                                                                              |
| 3.0.0        | [3.0.x](https://docs.vmware.com/en/VMware-SQL-with-MySQL-for-Tanzu-Application-Service/3.0/mysql-for-tas/about_mysql_vms.html)   |
| 2.10         | [2.10.x](https://docs.vmware.com/en/VMware-SQL-with-MySQL-for-Tanzu-Application-Service/2.10/mysql-for-tas/about_mysql_vms.html) |
| 2.9          | [2.9.x](https://docs.vmware.com/en/VMware-SQL-with-MySQL-for-Tanzu-Application-Service/2.9/mysql-for-tas/about_mysql_vms.html)   |
| 2.8, earlier | No longer used                                                                                                                   |

**Note**: Branches v1.9 through v1.4, v2.0 through v2.9 are no longer published as live HTML documentation. However, documentation for those versions of PDFs is available as PDFs.

## Where is the book repository?

The private book repo associated with this content repo is [**docs-book-mysql**](https://github.com/pivotal-cf/docs-book-mysql).

## Partials

Cross-product partials for **VMware SQL with MySQL for Tanzu Application Service** are single sourced from the [Services Partials](https://github.com/pivotal-cf/docs-partials) repo.

Previously, these partials were sourced from the v018.x branch of the [On Demand Service Broker SDK](https://github.com/pivotal-cf/docs-on-demand-service-broker/tree/v0.18.x) content repository.

## Style Guide

This is a word list for terminology and word usage specific to the VMware SQL with MySQL for Tanzu Application Service docs.

| Word | Explanation |
|------|-------------|
| adbr plugin | This is the short form of the ApplicationDataBackupRestore plugin. Use the short form in headings and after the first use in body text. |
| ApplicationDataBackupRestore (adbr) plugin | First use in body text for the adbr plugin. |
| CA certificate | A primary(?) certificate that is signed by a certificate authority. |
| certificate | generic word, there are TLS certificates, internal certificates, CA certificates |
| highly available cluster | No need to capitalize. Don't mix with "high availability cluster" or Galera. Galera happens to be the technology right now, but highly available cluster is the topology. You can abbreviate to HA cluster after spelling out on first use. Don't use "HAC". |
| internal certificate | These are certificates managed by VMware SQL with MySQL for Tanzu Application Service. There are many of these. They get rotated with upgrades. |
| leader-follower | It seems that we always hyphenate it. But we don't capitalize it. |
| MySQL database cluster node | Only applies to Galera clusters. Spell out like this at first use, then opt for "node" on the rest of the page. |
| node | See above. The old proxy page used: MySQL node, server node, MySQL servers, cluster back-ends, back-end database cluster node, database node VM, proxy instance, proxy. But, only two things are referred to here, nodes and proxies. |
| proxy | Use proxy consistently instead of alternating between "proxy instance" and "proxy". |
| TLS certificates | Certificates needed if you use TLS; there are two or three of these. One is the TLS CA certificate. |
| TLS CA certificate | The CA certificated used for TLS. You can add your own or use the one that  Ops Manager provides. |
|Multi-Site Replication| This is the name of the feature and plan in the UI in v2.7.2 and later|
|multi-site replication service key| When referring to a service key that is only used for nodes in different foundations or data centers to communicate, use this term at least on first use. If you are referring to a "normal" service key, like one that is used for TLS, just use `service key`|


## Steps for local development
```
$ git clone git@github.com:pivotal-cf/docs-layout-repo
$ git clone git@github.com:pivotal-cf/docs-mysql
$ cd docs-mysql && git checkout <branch> && cd -
$ git clone git@github.com:pivotal-cf/docs-book-mysql
$ cd docs-book-mysql
$ bundle install
$ bundle exec bookbinder watch
$ open http://127.0.0.1:XXXX/p-mysql/<branch>
```
