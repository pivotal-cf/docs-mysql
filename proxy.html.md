---
title: Proxy for MySQL for Pivotal Cloud Foundry
owner: MySQL
---


In Pivotal MySQL for Cloud Foundry&reg;, [Switchboard](https://github.com/cloudfoundry-incubator/switchboard) is used to proxy TCP connections to healthy MariaDB nodes.

A proxy is used to gracefully handle failure of MariaDB nodes. Use of a proxy permits very fast, unambiguous failover to other nodes within the cluster in the event of a node failure.

When a node becomes unhealthy, the proxy re-routes all subsequent connections to a healthy node. All existing connections to the unhealthy node are closed.

## <a id="proxy-dashboard"></a>Proxy Dashboard ##

The service provides a dashboard where administrators can observe health and metrics for each instance in the proxy tier. Metrics include the number of client connections routed to each backend database cluster node.

The dashboard for each proxy instance can be found at: `http://proxy-<job index>-p-mysql.<system-domain>`. The job index starts at 0. For example, if you have two proxy instances deployed and your system-domain is `example.com`, dashboards would be accessible at:

- [http://proxy-0-p-mysql.example.com](http://proxy-0-p-mysql.example.com)
- http://proxy-1-p-mysql.example.com

Basic auth credentials are required to access the dashboard. These can be found in the Credentials tab of the MySQL product in Operations Manager.

## <a id="consistent-routing"></a>Consistent Routing ##

At any given time, Switchboard will only route to one active node. That node will continue to be the only active node until it becomes unhealthy.

If multiple Switchboard proxies are used in parallel (ex: behind a load-balancer) there is no guarantee that the proxies will choose the same active node. This can result in deadlocks, wherein attempts to update the same row by multiple clients will result one commit succeeding and the other fails. This is a known issue, with exploration of mitigation options on the roadmap for this product. To avoid this problem, use a single proxy instance or an external failover system to direct traffic to one proxy instance at a time.


## <a id="node-health"></a>Node Health ##

### <a id="healthy"></a>Healthy ###

The proxy queries an HTTP healthcheck process, co-located on the database node, when determining where to route traffic.

If the healthcheck process returns HTTP status code of 200, the node is added to the pool of healthy nodes.

A resurrected node will not immediately receive connections. The proxy will continue to route all connections, new or existing, to the currently active node. In the case of failover, all healthy nodes will be considered as candidates for new connections.

### <a id="unhealthy"></a>Unhealthy ###

If the healthcheck returns HTTP status code 503, the node is considered unhealthy.

This happens when a node becomes non-primary, as specified by the [cluster-behavior docs](cluster-behavior.html).

The proxy will sever all existing connections to newly unhealthy nodes. Clients are expected to handle reconnecting on connection failure. The proxy will route new connections to a healthy node, assuming such a node exists.

### <a id="unresponsive"></a>Unresponsive ###

If node health cannot be determined due to an unreachable or unresponsive healthcheck endpoint, the proxy will consider the node unhealthy. This may happen if there is a network partition or if the VM containing the healthcheck and MariaDB node died.


## <a id="proxy-count"></a>Proxy count ##

If the operator sets the total number of proxies to 0 hosts in OpsManager or BOSH deployment manifest, then applications will connect directly to one healthy MariaDB node making that node a single point of failure for the cluster.

The recommended number of proxies are 2; this provides redundancy should one of the proxies fail.

## <a id="proxy-spof"></a>Removing the proxy as a SPOF ##

The proxy tier is responsible for routing connections from applications to healthy MariaDB cluster nodes, even in the event of node failure.

Bound applications are provided with a hostname or IP address to reach a database managed by the service. By default, the MySQL service will provide bound applications with the IP of the first instance in the proxy tier. Even if additional proxy instances are deployed, client connections will not be routed through them. This means the first proxy instance is a single point of failure.

**In order to eliminate the first proxy instance as a single point of failure, operators must configure a load balancer to route client connections to all proxy IPs, and configure the MySQL service to give bound applications a hostname or IP address that resolves to the load balancer.**

### <a id="lb-config"></a>Configuring load balancer ###

Configure the load balancer to route traffic for TCP port 3306 to the IPs of all proxy instances on TCP port 3306. Next, configure the load balancer's healthcheck to use the proxy health port. This is TCP port 1936 by default to maintain backwards compatibility with previous releases. This port is not configurable.

### <a id="lb-proxy-config"></a>Configuring p-mysql to give applications the address of the load balancer
To ensure that bound applications will use the load balancer to reach bound databases, navigate to the p-mysql tile in Operations Manager, then the Resource Config configuration screen within it. **On AWS only**, enter your load balancer's hostname in the "ELB Names" column for the Proxy row.

### <a id="route-53"></a>AWS Route 53 ###

To set up a Round Robin DNS across multiple proxy IPs using AWS Route 53,
follow the following instructions:

1. Log in to AWS.
2. Click Route 53.
3. Click Hosted Zones.
4. Select the hosted zone that contains the domain name to apply round robin routing to.
5. Click 'Go to Record Sets'.
6. Select the record set containing the desired domain name.
7. In the value input, enter the IP addresses of each proxy VM, separated by a newline.

Finally, update the manifest property `properties.mysql_node.host` for the cf-mysql-broker job, as described above.

## <a id="switchboard-api"></a> API

The proxy hosts a JSON API at `proxy-<bosh job index>.p-mysql.<system domain>/v0/`.

The API provides the following route:

Request:
*  Method: GET
*  Path: `/v0/backends`
*  Params: ~
*  Headers: Basic Auth

Response:

```
[
  {
    "name": "mysql-0",
    "ip": "1.2.3.4",
    "healthy": true,
    "active": true,
    "currentSessionCount": 2
  },
  {
    "name": "mysql-1",
    "ip": "5.6.7.8",
    "healthy": false,
    "active": false,
    "currentSessionCount": 0
  },
  {
    "name": "mysql-2",
    "ip": "9.9.9.9",
    "healthy": true,
    "active": false,
    "currentSessionCount": 0
  }
]
```

---

For more information about SwitchBoard, read the [proxy documentation](proxy.html)
