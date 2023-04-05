docs-mysql
==========

Docs for VMware SQL with MySQL for Tanzu Application Service

## Which branch to use?

**Note**: Provide instructions in your PRs to indicate which branches you want Docs to apply your commits to.

| Branch name | Use for… |
|-------------| -------|
| master      | "edge" branch for 2.x, publishes to https://docs-pcf-staging.cfapps.io/p-mysql/2-n/|
| 2.9         | [v2.9.x](https://docs.pivotal.io/p-mysql/2-9) |
| 2.8.3-174881244 | This is a temporary feature branch to prep for v2.8.3. |
| 2.8         | v2.8.x |
| 2.7         | v2.7.x |
| 2.6         | v2.6.x |
| 2.5         | DEPRECATED. DO NOT USE. |
| 2.4         | DEPRECATED. DO NOT USE. |
| 2.3         | DEPRECATED. DO NOT USE. |
| 2.2         | v2.2.x — PDFed: https://docs.pivotal.io/archives/mysql-docs-2.2.pdf |
| 2.1         | v2.1.x — PDFed: https://docs.pivotal.io/archives/mysql-docs-2.1.pdf |
| 2.0         | v2.0.x — PDFed: https://docs.pivotal.io/archives/mysql-docs-2.0.pdf |
| 1.11        | There are no plans for a v1.11. However, because it publishes to the edge branch, you could use it to stage big changes for the v1.10.x without worrying they'll go to production prematurely. |
| 1.10        | DEPRECATED. DO NOT USE. |
| 1.9         | v1.9.x — PDFed: https://docs.pivotal.io/archives/mysql-docs-1.9.pdf |

**Note**: Branches v1.9 through v1.4, v2.0, v2.1 and v2.2 are no longer published as live documentation. However, documentation for those versions of PDFs is available as PDFs.

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
