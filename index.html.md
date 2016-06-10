---
title: MySQL for Pivotal Cloud Foundry&reg;
owner: MySQL
---

This is documentation for the MySQL service for [Pivotal Cloud Foundry&reg;](https://network.pivotal.io/products/pivotal-cf) (PCF).

## Product Snapshot

Current MySQL for Pivotal Cloud Foundry&reg; Details
<div style="line-height: 1; padding-left: 3em">

- **Version**: 1.7.8
- **Release Date**: 2016-05-18
- **Software component versions**: MariaDB 10.0.21, Galera 25.3.9
- **Compatible Ops Manager Version(s)**: 1.5.x, 1.6.x, 1.7.x
- **Compatible Elastic Runtime Version(s)**: 1.5.x, 1.6.x, 1.7.x
- **vSphere support?** Yes
- **AWS support?** Yes
- **OpenStack support?** Yes

</div>

## Upgrading to the Latest Version
Consider the following compatibility information before upgrading MySQL for Pivotal Cloud Foundry&reg;.

For more information, refer to the full [Product Version Matrix](http://docs.pivotal.io/compatibility-matrix.pdf).

<table border="1" class="nice">
<tr>
	<th rowspan="2">Ops Manager Version</th>
	<th colspan="2">Supported Upgrades from Imported MySQL Installation</th>
</tr>

<tr>
	<th>From</th>
	<th>To</th>
<tr>

<tr>
	<th rowspan="2">1.3.x</th>
	<td>1.2</td>
	<td>1.3</td>
	<tr>
		<td>1.3.2</td>
		<td>1.4.0</td>
	</tr>
</tr>

<tr>
	<th rowspan="2">1.4.x and 1.5.x</th>
	<td rowspan="2">1.3.2</td>
	<td>1.4.0</td>
	<tr>
		<td>1.5.0</td>
	</tr>
</tr>


<tr>
	<th rowspan="9">1.4.x, 1.5.x<br>1.6.x, 1.7.x</th>
	<td>1.4.0</td>
	<td>1.5.0</td>

	<tr>
		<td>1.5.0</td>
		<td>1.6.1 - 1.6.11</td>
	</tr>

	<tr>
		<td rowspan="2">1.6.1 - 1.6.11</td>
		<td>Next 1.6.X release - 1.7.8</td>
	  <tr>
		  <td>1.8.0-edge.1 - 1.8.0-edge.6</td>
	  </tr>
	</tr>

	<tr>
		<td rowspan="2">1.7.0 - 1.7.7</td>
		<td>Next 1.7.X release - 1.7.8</td>
	  <tr>
		  <td>1.8.0-edge.1 - 1.8.0-edge.6</td>
	  </tr>
	</tr>

	<tr>
		<td>1.7.8</td>
		  <td>1.8.0-edge.1 - 1.8.0-edge.6</td>
	</tr>
</tr>

</table>

## <a id="release-notes"></a>Release Notes ##

Consult the [Release Notes](release-notes.html) for information about changes between versions of this product.

## <a id="overview"></a> Overview ##

The p-mysql product delivers a fully managed, "Database as a Service" to Cloud Foundry users. When installed, the tile deploys and maintains a single or three-node cluster running a recent release of [MariaDB](http://www.mariadb.org), SQL Proxies for super-fast failover, and Service Brokers for Cloud Foundry integration. We work hard to ship the service configured with sane defaults, following the principle of least surprise for a general-use relational database service.

When installed, Developers can attach a database to their applications in as little as two commands, `cf create-service` and `cf bind-service`. Connection credentials are automatically provided in the [standard manner](https://docs.pivotal.io/pivotalcf/devguide/deploy-apps/environment-variable.html#VCAP-SERVICES). Developers can select from a menu of service plans options, which are configured by the platform operator.

Two configurations are supported:

<center>
<table>
  <tr><th></th><th>Single</th><th>Highly Available</th></tr>
  <tr><td>**MySQL**</td><td>1 node</td><td>3-node cluster</td></tr>
  <tr><td>**SQL Proxy**</td><td>1 node</td><td>2 nodes</td></tr>
  <tr><td>**Service Broker**</td><td>1 node</td><td>2 nodes</td></tr>
  <tr><td>High Availability</td><td>-</td><td>Yes</td></tr>
  <tr><td>Multi-AZ Support</td><td>-</td><td>Yes</td></tr>
  <tr><td>Rolling Upgrades</td><td>-</td><td>Yes</td></tr>
  <tr><td>Automated Backups</td><td>Yes</td><td>Yes</td></tr>
  <tr><td>Customizable Plans</td><td>Yes</td><td>Yes</td></tr>
  <tr><td>Customizable VM Instances</td><td>Yes</td><td>Yes</td></tr>
  <tr><td>Plan Migrations</td><td>Yes</td><td>Yes</td></tr>
  <tr><td>Encrypted Communication</td><td>Yes &#x271D;</td><td>Yes &#x271D;</td></tr>
  <tr><td>Encrypted Data at-rest</td><td>-</td><td>-</td></tr>
  <tr><td>Long-lived Canaries</td><td>-</td><td>-</td></tr>
</table>
</center>

(&#x271D;) Requires IPSEC BOSH plug-in

## <a id="limitations"></a>Limitations ##

- Single and three-node clusters are the only supported topologies. OpsManager will allow the Operator to set the number of instances to other values, only one and three are advised. Please see the [note](cluster-behavior.html#even-number) in the Cluster Behavior document.
- Although two Proxy instances are deployed by default, there is no automation to direct clients from one to the other. See the note in the [Proxy](#proxy) section, as well as the entry in [Known Issues](known-issues.html).
- Only the InnoDB storage engine is supported; it is the default storage engine for new tables. Attempted use of other storage engines (including MyISAM) may result in data loss.
- All databases are managed by shared, multi-tenant server processes. Although data is securely isolated between tenants using unique credentials, application performance may be impacted by noisy neighbors.
- Round-trip latency between database nodes must be less than five seconds; if the latency is higher than this, nodes will become partitioned. If more than half of cluster nodes are partitioned, the cluster will lose quorum and become unusable until manually bootstrapped.
- See also the list of [Known Limitations](https://mariadb.com/kb/en/mariadb/mariadb-galera-cluster-known-limitations/) in MariaDB cluster.

## <a id="known-issues"></a>Known Issues ##

Consult the [Known Issues](known-issues.html) topic for information about issues in current releases of p-mysql.

## <a id="installation"></a>Installation ##

1. Download the product file from [Pivotal Network](https://network.pivotal.io/products/p-mysql).
1. Upload the product file to your Ops Manager installation.
1. Click **Add** next to the uploaded product description in the Available Products view
   to add this product to your staging area.
1. Click the newly added tile to review configurable [Settings](#settings).
1. Click **Apply Changes** to deploy the service.

## <a id="settings"></a>Settings ##

### <a id="service-plan"></a>Service Plan ###

A single service plan enforces quotas of 100 megabytes of storage per database and 40 concurrent connections per user by default. Users of Operations Manager can configure these plan quotas. Changes to quotas will apply to all existing database instances as well as new instances. In calculating storage utilization, indexes are included along with raw tabular data.

The name of the plan is **100mb-dev** by default and is automatically updated if the storage quota is modified. Thus, if the storage quota is changed to 1024 megabytes, the new default plan name will be **1024mb-dev**.

**Note**: After changing a plan's definition, all instances of the plan must be updated. For each plan, either the operator or the user must run `cf update-service SERVICE_INSTANCE -p NEW_PLAN_NAME` on the command line.

**Further Note**: This feature does not work properly in versions of p-mysql 1.6.3 and earlier. See the entry in [Known Issues](known-issues.html) for the recommended workaround.

Provisioning a service instance from this plan creates a MySQL database on a multi-tenant server, suitable for development workloads. Binding applications to the instance creates unique credentials for each application to access the database.

### <a id="proxy"></a>Proxy ###

The proxy tier is responsible for routing connections from applications to healthy MariaDB cluster nodes, even in the event of node failure.

Applications are provided with a hostname or IP address to reach a database managed by the service. For more information, see [Application Binding](http://docs.pivotal.io/pivotalcf/devguide/services/index.html#application-binding). By default, the MySQL service will provide bound applications with the IP of the first instance in the proxy tier. Even if additional proxy instances are deployed, client connections will not be routed through them. This means the first proxy instance is a single point of failure.

**In order to eliminate the first proxy instance as a single point of failure, operators must configure a load balancer to route client connections to all proxy IPs, and configure the MySQL service to give bound applications a hostname or IP address that resolves to the load balancer.**

#### Configuring a load balancer

In older versions of the product, applications were given the IP of the single MySQL server in bind credentials. When upgrading to v1.5.0, existing applications will continue to function, but, to take advantage of high availability features, they must be rebound to receive either the IP of the first proxy instance or the IP/hostname of a load balancer.

In order to configure a load balancer with the IPs of the proxy tier before v1.5.0 is deployed and prevent applications from obtaining the IP of the first proxy instance, the product enables an operator to configure the IPs that will be assigned to proxy instances. The following instructions applies to the **Proxy** settings page for the MySQL product in Operation Manager.

- In the **Proxy IPs** field, enter a list of IP addresses that should be assigned to the proxy instances. These IP addresses must be in the CIDR range configured in the Director tile and not be currently allocated to another VM. Look at the **Status** pages of other tiles to see what IP addresses are in use.

- In the **Binding Credentials Hostname** field, enter the hostname or IP address that should be given to bound applications for connecting to databases managed by the service. This hostname or IP address should resolve to your load balancer and be considered long-lived. When this field is modified, applications must be rebound to receive updated credentials.

Configure your load balancer to route connections for a hostname or IP to the proxy IPs. As proxy instances are not synchronized, we recommend configuring your load balancer to send all traffic to one proxy instance at a time until it fails, then failover to another proxy instance. For details, see [Known Issues](#known-issues).

**Important**: To configure your load balancer with a healthcheck or monitor, use TCP against port **1936**. Unauthenticated healthchecks against port 3306 will cause the service to become unavailable, and will require manual intervention to fix.

#### Adding a load balancer after an initial deploy

If v1.5.0 is initially deployed without a load balancer and without proxy IPs configured, a load balancer can be setup later to remove the proxy as a single point of failure. However, there are several implications to consider:

- Applications will have to be rebound to receive the hostname or IP that resolves to the load balancer. To rebind: unbind your application from the service instance, bind it again, then restage your application. For more information see [Managing Service Instances with the CLI](http://docs.pivotal.io/pivotalcf/devguide/services/managing-services.html). In order to avoid unnecessary rebinding, we recommend configuring a load balancer before deploying v1.5.0.
- Instead of configuring the proxy IPs in Operations manager, use the IPs that were dynamically assigned by looking at the **Status** page. Configuration of proxy IPs after the product is deployed with dynamically assigned IPs is not well supported; see [Known Issues](#known-issues).

### <a id="lifecycle-errands"></a>Lifecycle Errands ###

Two lifecycle errands are run by default: the **broker registrar** and the **smoke test**. The broker registrar errand registers the broker with the Cloud Controller and makes the service plan public. The smoke test errand runs basic tests to validate that service instances can be created and deleted, and that applications pushed to Elastic Runtime can be bound and write to MySQL service instances. Both errands can be turned on or off on the **Lifecycle Errands** page under the **Settings** tab.

### <a id="resource-config"></a>Resource Config ###

#### <a id="instance-capacity"></a>Instance Capacity ####

An operator can configure how many database instances can be provisioned (instance capacity) by configuring the amount of persistent disk allocated to the MySQL server nodes. The broker will  provision a requested database if there is sufficient unreserved persistent disk. This can be managed using the Persistent Disk field for the MySQL Server job in the Resource Config setting page in Operations Manager. Not all persistent disk will be available for instance capacity; about 2-3 GB is reserved for service operation. Adding nodes to the cluster increases durability, not capacity. Multiple backend clusters, to increase capacity or for isolation, are not yet supported.

In determining how much persistent disk to make available for databases, operators should also consider that MariaDB servers require sufficient CPU, RAM, and IOPS to promptly respond to client requests for all databases.

## <a id="provision-and-bind"></a>Provisioning and Binding via Cloud Foundry ##

As part of installation the product is automatically registered with [Pivotal Cloud Foundry&reg;](https://network.pivotal.io/products/pivotal-cf) Elastic Runtime (see [Lifecycle Errands](#lifecycle-errands)). On successful installation, the MySQL service is available to application developers in the Services Marketplace, via the web-based Developer Console or `cf marketplace`. Developers can then provision instances of the service and bind them to their applications:

<pre class="terminal">
$ cf create-service p-mysql 100mb-dev mydb
$ cf bind-service myapp mydb
$ cf restart myapp
</pre>

For more information about the use of services, see the [Services Overview](http://docs.pivotal.io/pivotalcf/devguide/services/).

## <a id="example-app"></a>Example Application ##

To help application developers get started with MySQL for PCF, we have provided an example application, which can be [downloaded here][example-app]. Instructions can be found in the included README.

[example-app]:mysql-example-app.tgz

## <a id="dashboard"></a>Service Instance Dashboard ##

Cloud Foundry users can access a dashboard for each MySQL service instances via SSO from Apps Manager. The dashboard displays current storage utilization of the database and the plan quota for storage. On the Space page in Apps Manager, users with the SpaceDeveloper role will find a **Manage** link next to the instance. Clicking this link will log users into the service dashboard via SSO.

## <a id="proxy-dashboard"></a>Proxy Dashboard ##

The service provides a dashboard where administrators can observe health and metrics for each instance in the proxy tier. Metrics include the number of client connections routed to each backend database cluster node.

The dashboard for each proxy instance can be found at: `http://proxy-<job index>.p-mysql.<system-domain>`. Job index starts at 0 so if you have two proxy instances deployed and your system-domain is `example.com`, dashboards would be accessible at `http://proxy-0.p-mysql.example.com` and `http://proxy-1.p-mysql.example.com`.

Basic auth credentials are required to access the dashboard. These can be found in the Credentials tab of the MySQL product in Operations Manager.

For more information about SwitchBoard, read the [proxy documentation](proxy.html)

## <a id="SeeAlso"></a>See Also##

  * [Notes on cluster configuration](cluster-config.html)
  * [Backing Up MySQL for PCF](backup.html)<br>
    **Note**: For information about backing up your PCF installation, refer to [Backing Up and Restoring Pivotal Cloud Foundry&reg;](http://docs.pivotal.io/pivotalcf/customizing/backup-restore/index.html).
  * [Determining MySQL cluster state](cluster-state.html)
  * [More on Cluster Scaling, Node Failure, and Quorum](cluster-behavior.html)
  * [Bootstrapping an ailing MySQL cluster](bootstrapping.html)
  * [Scaling down a MySQL cluster](scaling-down.html)
