---
title: Release Notes
owner: MySQL
---

## <a id="1-8-0-e5"></a>1.8.0-Edge.5

Release date: 06 May 2016

- Updated stemcell to 3232.2 This is a security upgrade that resolves the following:
  - [USN-2959-1](http://www.ubuntu.com/usn/usn-2959-1/)
- **Known Issue:** On PCF 1.7 only, the p-mysql acceptance test errand will not work on p-mysql 1.8.0-edge.1 through 1.8.0-edge.5. This will be fixed in the next release.

Additional information can be found at https://pivotal.io/security.

## <a id="1-8-0-e4"></a>1.8.0-Edge.4

Release date: 8 April 2016

- Automated backups can now additionally target a Ceph back-end storage service or direct to another host via SCP.
- New `Database Configuration` settings page
  - Includes the option to enable a read-only user.
  - Allows an override to activate DNS reverse name resolution.
- Additional mysql server tuning tweaks to improve performance.
- MariaDB updated to version 10.0.23.
- **Bug fix**: Update `broker-registrar` to avoid runaway CPU condition on broker VMs.
- **Bug fix:** Operator can now override the default number of open files while taking backups.
- **Bug fix:** Allow Operator to override S3 endpoint default.

## <a id="1-8-0-e3"></a>1.8.0-Edge.3

Release date: 16 March 2016

- See below, same security update as [version 1.6.9](#1-6-9)

## <a id="1-8-0-e2"></a>1.8.0-Edge.2

Release date: 02 March 2016

- See below, same security update as [version 1.6.8](#1-6-8)

## <a id="1-8-0-e1"></a>1.8.0-Edge.1

Release date: 20 January 2016

- **New Feature:** Multiple service plans
  - **Note:** On upgrade from a version of p-MySQL that offered only a single plan, the default plan will be renamed. Regardless of the name of the previous plan (e.g., `100mb-dev`), the plan will now be named, `pre-existing-plan`. It's not possible to automatically reset the plan to the former name. If you wish to retain the same plan name, it's fine to edit that plan name before clicking 'Apply Changes' when upgrading to p-MySQL v1.8.0. See [the documentation](./index.html#service-plan) for more information.
- **New Feature:** `bosh` bootstrap errand
- MariaDB updated to version 10.0.22 and Galera 25.3.9.

## <a id="1-7-7"></a>1.7.7

- See below, same update as [version 1.6.10](#1-6-10)

## <a id="1-6-10"></a>1.6.10

Release date: 06 May 2016

- Updated stemcell to 3146.11 This is a security upgrade that resolves the following:
  - [USN-2959-1](http://www.ubuntu.com/usn/usn-2959-1/)
- **Bug fix**: Updated acceptance tests to pass on PCF-1.7.
- **Bug fix**: Update `broker-registrar` to avoid runaway CPU condition on broker VMs.

Additional information can be found at https://pivotal.io/security.

## <a id="1-7-6"></a>1.7.6

- See below, same update as [version 1.6.9](#1-6-9)

## <a id="1-6-9"></a>1.6.9

Release date: 16 March 2016

- Updated stemcell to 3146.10. This is a security upgrade that resolves the following:
  - [USN-2929-1](http://www.ubuntu.com/usn/usn-2929-1/)

Additional information can be found at https://pivotal.io/security.

## <a id="1-7-5"></a>1.7.5

- See below, same security update as [version 1.6.8](#1-6-8)

## <a id="1-6-8"></a>1.6.8

Release date: 24 February 2016

- Updated stemcell to 3146.9. This is a security upgrade that resolves the following:
  - [USN-2910-1](http://www.ubuntu.com/usn/usn-2910-1/)

## <a id="1-7-4"></a>1.7.4

- See below, same security update as [version 1.6.7](#1-6-7)

## <a id="1-6-7"></a>1.6.7

Release date: 19 February 2016

- Updated stemcell to 3146.8. This is a security upgrade that resolves the following:
  - [USN-2900-1](http://www.ubuntu.com/usn/usn-2900-1), a critical GNU C lib (glibc) CVE
  - [USN-2897-1](http://www.ubuntu.com/usn/usn-2897-1)
  - [USN-2896-1](http://www.ubuntu.com/usn/usn-2896-1)

Additional information can be found at https://pivotal.io/security.

## <a id="1-7-3"></a>1.7.3

- See below, same security update as [version 1.6.6](#1-6-6)

## <a id="1-6-6"></a>1.6.6

Release date: 2 February 2016

- Updated stemcell to 3146.6. This is a security upgrade that resolves the following:
  - [USN-2882-1](http://www.ubuntu.com/usn/usn-2882-1)
  - [USN-2879-1](http://www.ubuntu.com/usn/usn-2879-1)
  - [USN-2875-1](http://www.ubuntu.com/usn/usn-2875-1)
  - [USN-2874-1](http://www.ubuntu.com/usn/usn-2874-1)
  - [USN-2871-1](http://www.ubuntu.com/usn/usn-2871-1)
  - [USN-2868-1](http://www.ubuntu.com/usn/usn-2868-1)
  - [USN-2865-1](http://www.ubuntu.com/usn/usn-2865-1)
  - [USN-2861-1](http://www.ubuntu.com/usn/usn-2861-1)

Additional information can be found at [https://pivotal.io/security](https://pivotal.io/security).

## <a id="1-7-2"></a>1.7.2

- See below, same security update as [version 1.6.5](#1-6-5)

## <a id="1-6-5"></a>1.6.5

Release date: 18 January 2016

- Updated stemcell to 3146.3. This is a security upgrade that resolves the following:
  - [USN-2869-1](http://www.ubuntu.com/usn/usn-2869-1)
  - [CVE-2016-0715](https://pivotal.io/security/cve-2016-0715).

Additional information can be found at https://pivotal.io/security.

## <a id="1-7-1"></a>1.7.1

- See below, same security update as [version 1.6.4](#1-6-4)

## <a id="1-6-4"></a>1.6.4

Release date: 07 January 2016

- Updated stemcell to 3146.2. This is a security upgrade that resolves the following Ubuntu Security Notices:
  - USN-2857-1, USN-2842-1, USN-2842-2, USN-2836-1, USN-2834-1, USN-2830-1, and USN-2829-1

## <a id="1-7-0-4"></a>1.7.0.4

- See below, same security update as [version 1.6.3.4](#1-6-3-4)

## <a id="1-6-3-4"></a>1.6.3.4

Release date: 04 December 2015

- Addresses an issue where changing the maximum number of allowed connections in the service plan does not affect the maximum number of allowed connections in service instances, new or existing. Note that the [Known Issue](known-issues.html) for Changing Service Plan Definition still applies; you'll still need to run the manual workaround for existing instances. Please look for improvements in a future release of p-mysql; we are sorry for the inconvenience.

- Updated stemcell to 3146. This is a security upgrade that resolves the following Ubuntu Security Notices:
  - [[USN-2821-1](http://www.ubuntu.com/usn/usn-2821-1/)] GnuTLS vulnerability

## <a id="1-7-0-3"></a>1.7.0.3

- See below, same security update as [version 1.6.3.3](#1-6-3-3)

## <a id="1-6-3-3"></a>1.6.3.3

Release date: 02 December 2015

- Updated stemcell to 3144. This is a regular security upgrade that resolves the following Ubuntu Security Notices:
  - [[USN-2815-1](http://www.ubuntu.com/usn/usn-2815-1/)] libpng vulnerabilities
  - [[USN-2812-1](http://www.ubuntu.com/usn/usn-2812-1/)] libxml2 vulnerabilities
  - [[USN-2810-1](http://www.ubuntu.com/usn/usn-2810-1/)] Kerberos vulnerabilities

## <a id="1-7-0-2"></a>1.7.0.2

- See below, same security update as [version 1.6.3.2](#1-6-3-2)

## <a id="1-6-3-2"></a>1.6.3.2

Release date: 11 November 2015

- Updated stemcell to 3130. This is a regular security upgrade that resolves the following issues:
  - [[USN-2806-1](http://www.ubuntu.com/usn/usn-2806-1/)] Linux kernel (Vivid HWE) vulnerability
  - [[USN-2798-1](http://www.ubuntu.com/usn/usn-2798-1/)] Linux kernel (Vivid HWE) vulnerabilities

## <a id="1-7-0-1"></a>1.7.0.1

- See below, same security update as [version 1.6.3.1](#1-6-3-1)

## <a id="1-6-3-1"></a>1.6.3.1

Release date: 3 November 2015

- Updated stemcell to 3112. This is a regular security upgrade that resolves the following issues:
  - [[USN-2778-1](http://people.canonical.com/~ubuntu-security/cve/2015/CVE-2015-5156.html)] Linux kernel (Vivid HWE) vulnerabilities

## <a id="1-7-0"></a>1.7.0

Release Date: 22 October 2015

- **New Feature**: Automated Operator-configured database [backups](backup.html) for Disaster Recovery.
- Updated MariaDB to version [10.0.21](https://mariadb.com/kb/en/mariadb/mariadb-10021-release-notes/) which also includes updates from MariaDB [10.0.20](https://mariadb.com/kb/en/mariadb/mariadb-10020-release-notes/).
- Updated stemcell to version 3100.
- **Security**: Fixes for CVE-2015-3900, a man-in-the-middle rubygems vulnerability.
- Bug fix: Switchboard fails to find recreated mysql node when ARP cache locked by hanging SYN_SENT.
- Bug fix: Every instance of Switchboard registrars the route `proxy-0.p-mysql.` rather than changing based on AZ index.

## <a id="1-6-3"></a>1.6.3

Release Date: 7 October 2015

- Updated stemcell to 3094. This is a regular security upgrade that resolves the following issues:
  - [[USN-2765-1](http://www.ubuntu.com/usn/usn-2765-1)] Linux kernel (Vivid HWE) vulnerability



## <a id="1-6-2"></a>1.6.2

Release Date: 4 September 2015

- Updated stemcell to 3062. This is a regular security upgrade that resolves the following issues:
  - [USN-2694-1] PCRE vulnerabilities
  - [USN-2698-1] SQLite vulnerabilities
  - [USN-2710-1] OpenSSH vulnerabilities
  - [USN-2710-2] OpenSSH regression
  - [USN-2718-1] Linux kernel (Vivid HWE) vulnerability

### Known Issues
- Experimental feature HTTPS traffic to HAProxy does not work; it will be fixed in an upcoming release.

## <a id="1-6-1"></a>1.6.1

- Updated stemcell to 3026 to resolve CVE-2015-3290


## <a id="1-6-0"></a>1.6.0

- Now includes MariaDB 10.0.19 and Galera 5.5.43 ([release notes](https://mariadb.com/kb/en/mariadb-galera-cluster-10019-release-notes/))
  - Includes several default configuration changes to better manage MariaDB's memory and disk usage during periods of heavy use.
- **Improved stability:** This version includes an all-new Quota Enforcer for enhanced stability and in preparation for new features in future releases.
- **Improved stability:** Now provides greater stability during cluster recovery by using the xtrabackup-v2 replication mechanism.
- Updates to both Service and Proxy dashboards to support the experimental HTTPS-only feature in Elastic Runtime 1.5
- Now uses the MariaDB connector rather than additionally including the MySQL connector.
- **Security:** The MySQL deployment now runs as user vcap, not root.
- **Security:** Upgraded Ruby and Rails components to address various CVEs.
- **Bug fix:** Once over quota, write privileges are not restored by dropping all tables.
- **Bug fix:** The broker-deregistrar errand now succeeds even when a MySQL service is broken.
- **Bug fix:** Service Broker dashboard should not return 500 if OAuth access token expires.

- **Upgrade support:** This product can be automatically upgraded from version 1.5.0

- Documentation now includes several new sections:
    * [Notes on cluster configuration](cluster-config.html)
    * [Determining MySQL cluster state](cluster-state.html)
    * [Background on Cluster Scaling, Node Failure, and Quorum](cluster-behavior.html)
    * [Bootstrapping an ailing MySQL cluster](bootstrapping.html)

* **Note:** BOSH Stemcell 3026 is required; this stemcell is provided by Ops Manager 1.5.1.

## <a id="1-5-0"></a>1.5.0

- **AWS support:** The clustered database service can now be deployed on Amazon Web Services from the Operations Manager Web UI.
  - Deployment is limited to a single Availability Zone. Look for multi-AZ in future releases.
  - Single availability zone is a limitation on AWS. Operations Manager on vSphere continues to support deployment to multiple availability zones.
  - The default instance type for the cluster nodes on AWS is m3.large.
  - All jobs are deployed with SSD for ephemeral and persistent disk.
- **IaaS agnostic**
  - The same product can be deployed to both AWS and vSphere
  - Precompiled packages are no longer included
  - p-mysql 1.5.0 requires Operations Manager 1.4.0
- **New proxy tier**
  - Improved availability: We have entirely re-written the proxy to eliminate situations where clients could hang when a cluster node was unhealthy.
  - A dashboard that clearly displays node health in real time
- **Upgrade support:** This product can be automatically upgraded from version 1.3.2 or 1.4.0
- **Cluster node resources increased for vSphere**: The default resources are now 4GB RAM, 2 CPU, 10GB persistent disk
- **Faster compilation:** Default resource for the compilation jobs on vSphere are now 4GB RAM, 4 CPU, 20GB persistent disk
- **Bug fix:** Fix broker-deregistrar errand to succeed even when MySQL service is broken
- **Bug fix:** Quota enforcer could fail when broker hasn't finished initializing

**Known issues:**

* On AWS, this version supports deployments in the US-East region. Multi-region support is coming in a future release.
* The experimental HTTPS-only feature in Elastic Runtime 1.5 may cause issues with this version of the product. Full support for HTTPS-only traffic is coming in a future release.
* Note: BOSH Stemcell 2865.1 is required for installation on Ops Manager 1.5.x and above.

## <a id="1-4-0"></a>1.4.0

- **High Availability**: database server is now clustered and synchronously replicated using MariaDB Galera Cluster. A copy of each database resides on all cluster nodes, and writes to any database are replicated to all copies. All client connections are routed to a primary cluster node, and in the event of a node failure the proxy tier manages failover, routing client connections to a healthy cluster node. MySQL server, proxy, and broker jobs can all be scaled out horizontally for increased availability, eliminating single points of failure.
- **Improved logging and monitoring**: route-registration on the broker is now an independent process
- **Bug fix**: calculation of storage utilization for the purposes of quota enforcement when multiple apps are bound
- **Bug fix**: format of jdbcUrl connection string (found in VCAP_SERVICES on bind)

    ### Notes on High Availability ###

- When upgrading from an older version, applications must be rebound to take advantage of high availability features. To rebind: unbind your application from the service instance, bind it again, then restage your application. For more information see [Managing Service Instances with the CLI](/pivotalcf/devguide/services/managing-services.html).
- Elimination of the proxy as a single point of failure requires configuration of an external load balancer to route connections to proxy instances. For details, see [Proxy Settings](/p-mysql/index.html#proxy).
- See [Known Issues](/p-mysql/#known-issues).

## <a id="1-3-2"></a>1.3.2

- **Updated stemcell addresses bash-shellshock vulnerabilities**: resolves CVEs discussed [here](http://www.pivotal.io/security/CVE-2014-6271) and [here](http://www.pivotal.io/security/CVE-2014-7186).

## <a id="1-3-0"></a>1.3.0

- **Syslog forwarding**: Syslogs are now streamed to the same host and port configured in Elastic Runtime settings
- **Dynamic instance capacity management**: Previously operators had to manually configure the maximum number of service instances permitted by the server. This required manual calculation and a knowledge of required system headroom. Admins can now manage instance capacity simply by adjusting persistent disk allocated to mysql nodes. Remaining instance capacity is determined dynamically by subtracting a safe estimate for system headroom and reserved storage for provisioned instances.
- **Trusty stemcell**: Server and broker are now deployed on Ubuntu “Trusty” 14.04 LTS stemcells, providing improved security, performance, and a smaller resource footprint.
- **Least necessary privileges**: The MySQL service dashboard uses a new, limited permission OAuth scope to determine whether a user currently has access to a service instance. The dashboard no longer has full read access to a user’s account.
- **Precompiled packages**: Most packages have been precompiled for the targeted stemcell. This will lower initial deployment times, at the cost of a larger download.

## <a id="1-2-0"></a>1.2.0

- Product renamed to 'MySQL for Pivotal CF'
- Plan attributes are configurable: max storage per database, max concurrent connections per user, and max databases
- Plan name is determined dynamically based on configured storage quota
- Plan features include disclaimer that the service is not for production use
- Developers can SSO to a service dashboard that displays storage utilization
- Security fixes including updates to Rails
- Service broker is registered by URL (rather than by IP). Typically has the format `https://p-mysql.<cf-domain>`.
* Lifecycle errands are used to register the broker and run tests that verify the deployment.
* Improved logging in service broker

### The following components will be re-deployed:

* cf-mysql-broker
* mysql

### New components:

* broker-registrar
* broker-deregistrar
* acceptance-tests

## <a id="1-1-0"></a>1.1.0

* Updated the format of metadata fields in the broker catalog endpoint and added
additional fields. For more information, see Catalog Metadata.
* Updated Ruby to version 2.0.0p353 to fix a vulnerability in 1.9.3p448.
* Requests to delete a service instance or binding now get a 200 response with an empty
JSON body instead of a 204.
* The broker now returns a clear error when there is no more capacity for additional
instances during a provision request. The response has status code `507`. The
user-facing error message is "Service plan capacity has been reached."

### The following components will be re-deployed:
* cf-mysql-broker
* mysql
