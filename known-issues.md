# Known Issues

### MySQL Backups to AWS S3
In versions of p-mysql 1.7.X and earlier, backups can only be sent to AWS S3 buckets that have been created in the [US Standard](http://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region) region, aka, "us-east-1." This limitation will be addressed in future releases.

### Elastic Runtime HTTPS-only feature
Support for the Experimental HTTPS-only feature is broken in p-mysql versions 1.6.X and earlier. The HTTPS-only feature works as designed in p-mysql 1.7.0 and later.

### Accidental deletion of a Service Plan

If and only if the Operator does all of these steps in sequence, a plan will become "unrecoverable":

1. Click the trash-can icon in the Service Plan screen
1. Enter a plan with the exact same name
1. Click the 'Save' button on the same screen
1. Return to the Ops Manager top-level, and click 'Apply Changes'

After clicking 'Apply Changes', the deploy will eventually fail with the error:
> Server error, status code: 502, error code: 270012, message: Service broker catalog is invalid: Plan names must be unique within a service

This unfortunate situation is unavoidable; once the Operator has committed via 'Apply Changes', the plan original plan cannot be recovered. For as long as service instances of that plan exist, you may not enter a new plan of the same name. At this point, the only workaround is to create a new plan with the same specifications, but specify a different name. Existing instances will continue to appear under the old plan name, but new instances will need to be created using the new plan name.

If you have committed steps 1 and 2, but not 4, no problem. Do not hit the 'Save' button. Simply return to the Installation Dashboard. Any accidental changes will be discarded.

If you have committed steps 1, 2 and 3, do not click 'Apply Changes.' Instead, return to the Installation Dashboard and click the '**Revert**' button. Any accidental changes will be discarded.

### Changing service plan definition
In p-mysql versions 1.7.0 and earlier, there is only one service plan. Changing the definition of that plan, the number of megabytes, number of connections, or both, will make it so that any new service instances will have those characteristics.

There is a bug in p-mysql versions 1.6.3 and earlier. Changing the plan does not change existing service instances. Existing plans will continue to be governed by the plan constraints effective at the time they were created. This is true regardless of whether or not an operator runs `cf update-service`.

There is a workaround for this bug, which will be resolved in future releases of p-mysql. In order for the change to be effective for existing plans, you must trigger this by interacting directly with the service broker:

```
curl -v -k -X PATCH https://BROKER_CREDS_USERNAME:BROKER_CREDS_PASSWORD@p-mysql.SYSTEM.DOMAIN/v2/service_instances/SERVICE_INSTANCE_ID?plan_id=17d793e6-6da6-4f0e-b58d-364a407166a0
```

- SYSTEM.DOMAIN is defined in OpsManager, under Elastic Runtime's **Settings** tab, in the `Cloud Controller` entry.
- BROKER\_CREDS\_USERNAME and BROKER\_CREDS\_PASSWORD are defined in OpsManager, under p-mysql's  **Credentials** tab, in the `Broker Auth Credentials` entry.
- To get each SERVICE\_INSTANCE\_ID, run `cf service INSTANCE --guid`. You should see output like this example:

  ```
  $ cf service acceptDB --guid
  4cae3a5e-66b1-4c9a-8536-feaff25237bf
  ```

Run this for each service instance to be updated.

**Furthermore**, if you have changed the max number of connections constraint, then it is necessary to update each bound application's setting directly from my mysql console. Follow these steps:
1. SSH into your Ops Manager director using these [instructions](https://docs.pivotal.io/pivotalcf/customizing/trouble-advanced.html#prepare).
1. Run `bosh deployments` to discover the name of your p-mysql deployment.
1. Run `bosh ssh` using your p-mysql's deployment name. Example: `bosh ssh mysql-partition-9d32f5601988152e869b/0`
1. Run `/var/vcap/packages/mariadb/bin/mysql -u root -p`.
  - The root user's password is defined in OpsManager, under p-mysql's **Credentials** tab.
1. Issue this MySQL command: `UPDATE mysql.user SET mysql.user.max_user_connections=NEW_MAX_CONN_VALUE WHERE mysql.user.User NOT LIKE '%root%' ;`
  - Make sure to change `NEW_MAX_CONN_VALUE` to whatever new setting you've chosen.
1. `exit;`

### Proxies may write to different MySQL masters
All proxy instances use the same method to determine cluster health. However, certain conditions may cause the proxy instances to route to different nodes, for example after brief cluster node failures.

This could be an issue for tables that receive many concurrent writes. Multiple clients writing to the same table could obtain locks on the same row, resulting in a deadlock. One commit will succeed and all others will fail and must be retried. This can be prevented by configuring your load balancer to route connections to **only one proxy instance at a time**.

### Number of proxy instances cannot be reduced.
Once the product is deployed with operator-configured proxy IPs, the number of proxy instances can not be reduced, nor can the configured IPs be removed from the **Proxy IPs** field. If instead the product is initially deployed without proxy IPs, IPs added to the **Proxy IPs** field will only be used when adding additional proxy instances, scaling down is unpredictably permitted, and the first proxy instance can never be assigned an operator-configured IP.

### Backups
In p-mysql 1.7.0, both `compressed` and `encrypted` show as `N` in the backup metadata file. This is due to the fact that p-mysql implements compression and encryption outside of the tool used to generate the file. This is a known defect, and will be corrected in future releases.

### MyISAM tables
The clustering plugin used in this release (Galera) does not support replication of MyISAM Tables. However, the service does not prevent the creation of MyISAM tables. When MyISAM tables are created, the tables will be created on every node (DDL statements are replicated), but data written to a node won't be replicated. If the persistent disk is lost on the node data is written to (for MyISAM tables only), data will be lost. To change a table from MyISAM to InnoDB, please follow this [guide](http://dev.mysql.com/doc/refman/5.5/en/converting-tables-to-innodb.html).

### Max user connections
When updating the max\_user\_connections property for an existing plan, the connections currently open will not be affected. For example, if you have decreased from 20 to 40, users with 40 open connections will keep them open. To force the changes upon users with open connections, an operator can restart the proxy job as this will cause the connections to reconnect and stay within the limit.  Otherwise, if any connection above the limit is reset it won't be able to reconnect, so the number of connections will eventually converge on the new limit.

### Long SST transfers
We provide a `database_startup_timeout` in our manifest which specifies how long to wait for the initial [SST](proxy.html#state-snapshot-transfer-sst) to complete (default is 150 seconds). If the SST takes longer than this amount of time, the job will report as failing. Versions before `cf-mysql-release v23` have a flaw in our startup script where it does not kill the mysqld process in this case. When monit restarts this process, it sees that mysql is still running and exits without writing a new pidfile. This means the job will continue to report as failing. The only way to fix this is to SSH onto the failing node, kill the mysqld process, and re-run `monit start mariadb_ctrl`.
