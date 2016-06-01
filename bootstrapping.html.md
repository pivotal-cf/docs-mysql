---
title: Bootstrapping a Galera Cluster
owner: MySQL
---

Bootstrapping is the process of (re)starting a Galera cluster.

## <a id="when-to-bootstrap"></a> When to Bootstrap ##

Bootstrapping is only required when the cluster has lost quorum.

Quorum is lost when less than half of the nodes can communicate with each other (for longer than the configured grace period). In Galera terminology, if a node can communicate with the rest of the cluster, its database is in a good state, and it reports itself as ```synced```.

If quorum has *not* been lost, individual unhealthy nodes should automatically rejoin the cluster once repaired (error resolved, node restarted, or connectivity restored).

<a id="lost-quorum"></a>

- Symptoms of Lost Quorum

    - All nodes appear "Unhealthy" on the proxy dashboard:
    ![3 out of 3 nodes are unhealthy.](quorum-lost.png)
    - All responsive nodes report the value of `wsrep_cluster_status` as `non-Primary`.

            mysql> SHOW STATUS LIKE 'wsrep_cluster_status';
            +----------------------+-------------+
            | Variable_name        | Value       |
            +----------------------+-------------+
            | wsrep_cluster_status | non-Primary |
            +----------------------+-------------+
    - All responsive nodes respond with `ERROR 1047` when using most statement types:

            mysql> select * from mysql.user;
            ERROR 1047 (08S01) at line 1: WSREP has not yet prepared node for application use

See [Cluster Behavior](cluster-behavior.html) for more details about determining cluster state.

## <a id="bootstrapping"></a>Bootstrapping ##

Bootstrapping requires you to run commands from the [Ops Manager Director](http://docs.pivotal.io/pivotalcf/customizing/index.html). Follow the instructions to use the [BOSH CLI](https://docs.pivotal.io/pivotalcf/customizing/trouble-advanced.html#cli) for command-line access.

<p class="note"><strong>Note:</strong> The examples in these instructions reflect a three-node p-mysql deployment. The process to bootstrap a two-node plus an arbitrator is identical, but the output will not match the examples.</p>

### <a id="assisted-bootstrap"></a>Assisted Bootstrap ###

1. P-mysql versions 1.8.0 and later include a [BOSH errand](http://bosh.io/docs/jobs.html#jobs-vs-errands) to automate the process of bootstrapping. It is still necessary to manually initiate the bootstrap process, but using this errand reduces the number of manual steps necessary to complete the process.

    In most cases, running the errand is sufficient, however there are some conditions which require additional steps.

    #### <a id="how-it-works"></a>How it works ####

    The bootstrap errand simply automates the steps in the manual bootstrapping process documented below. It finds the node with the highest transaction sequence number, and asks it to start up by itself (i.e. in bootstrap mode), then asks the remaining nodes to join the cluster.

    ### <a id="cluster-disrupted"></a>Scenario 1: Virtual Machines running, Cluster Disrupted ###

    1. If the nodes are up and running, but the cluster has been disrupted, the jobs will appear as `failing.` The output of `bosh instances` will look like this:

            $ bosh instances
            [...]
            +--------------------------------------------------+---------+------------------------------------------------+------------+
            | Instance                                         | State   | Resource Pool                                  | IPs        |
            +--------------------------------------------------+---------+------------------------------------------------+------------+
            | cf-mysql-broker-partition-a813339fde9330e9b905/0 | running | cf-mysql-broker-partition-a813339fde9330e9b905 | 10.0.16.61 |
            | cf-mysql-broker-partition-a813339fde9330e9b905/1 | running | cf-mysql-broker-partition-a813339fde9330e9b905 | 10.0.16.62 |
            | mysql-partition-a813339fde9330e9b905/0           | failing | mysql-partition-a813339fde9330e9b905           | 10.0.16.55 |
            | mysql-partition-a813339fde9330e9b905/1           | failing | mysql-partition-a813339fde9330e9b905           | 10.0.16.56 |
            | mysql-partition-a813339fde9330e9b905/2           | failing | mysql-partition-a813339fde9330e9b905           | 10.0.16.57 |
            | proxy-partition-a813339fde9330e9b905/0           | running | proxy-partition-a813339fde9330e9b905           | 10.0.16.59 |
            | proxy-partition-a813339fde9330e9b905/1           | running | proxy-partition-a813339fde9330e9b905           | 10.0.16.60 |
            +--------------------------------------------------+---------+------------------------------------------------+------------+

        In this situation, it is OK to immediately try the bootstrap errand:

        1. Log into the BOSH director
        1. Select the correct deployment
        1. `bosh run errand bootstrap`

        You will see many lines of output, eventually followed by:

        ```
        Bootstrap errand completed

        [stderr]
        + echo 'Started bootstrap errand ...'
        + JOB_DIR=/var/vcap/jobs/bootstrap
        + CONFIG_PATH=/var/vcap/jobs/bootstrap/config/config.yml
        + /var/vcap/packages/bootstrap/bin/cf-mysql-bootstrap -configPath=/var/vcap/jobs/bootstrap/config/config.yml
        + echo 'Bootstrap errand completed'
        + exit 0

        Errand `bootstrap' completed successfully (exit code 0)
        ```

        There are times when this won't work immediately. Unfortunately, sometimes it is best to wait and try again a few minutes later.


    ### <a id="vms-terminated"></a>Scenario 2: Virtual Machines Terminated or Lost ###
    1. In more severe circumstances, such as power failure, it's possible that all of your VMs have been lost. They'll need to be recreated before you can begin to recover the cluster. In this case, you'll see the nodes appear as `unknown/unknown` in the BOSH output.

            $ bosh instances
            +--------------------------------------------------+--------------------+------------------------------------------------+------------+
            | Instance                                         | State              | Resource Pool                                  | IPs        |
            +--------------------------------------------------+--------------------+------------------------------------------------+------------+
            | unknown/unknown                                  | unresponsive agent |                                                |            |
            +--------------------------------------------------+--------------------+------------------------------------------------+------------+
            | unknown/unknown                                  | unresponsive agent |                                                |            |
            +--------------------------------------------------+--------------------+------------------------------------------------+------------+
            | unknown/unknown                                  | unresponsive agent |                                                |            |
            +--------------------------------------------------+--------------------+------------------------------------------------+------------+
            | cf-mysql-broker-partition-e97dae91e44681e0b543/0 | running            | cf-mysql-broker-partition-e97dae91e44681e0b543 | 10.0.16.65 |
            | cf-mysql-broker-partition-e97dae91e44681e0b543/1 | running            | cf-mysql-broker-partition-e97dae91e44681e0b543 | 10.0.16.66 |
            +--------------------------------------------------+--------------------+------------------------------------------------+------------+
            | proxy-partition-e97dae91e44681e0b543/0           | running            | proxy-partition-e97dae91e44681e0b543           | 10.0.16.63 |
            | proxy-partition-e97dae91e44681e0b543/1           | running            | proxy-partition-e97dae91e44681e0b543           | 10.0.16.64 |
            +--------------------------------------------------+--------------------+------------------------------------------------+------------+

        #### <a id="vm-recovery"></a>VM Recovery ####

        VM Recovery is best left to OpsManager by configuring the VM [Resurrector](http://docs.pivotal.io/pivotalcf/customizing/resurrector.html#enabling). If enabled, the system will notice that the VMs are gone, and automatically attempt to recreate them. You will be able to see evidence of that by seeing the scan-and-fix job in the output of `bosh tasks recent --no-filter`.

            $ bosh tasks recent --no-filter
            +-----+------------+-------------------------+----------+--------------------------------------------+---------------------------------------------------+
            | #   | State      | Timestamp               | User     | Description                                | Result                                            |
            +-----+------------+-------------------------+----------+--------------------------------------------+---------------------------------------------------+
            | 123 | queued     | 2016-01-08 00:18:07 UTC | director | scan and fix                               |                                                   |

        If you have not configured the Resurrector to run automatically, you can also run the BOSH Cloud Check interactive command `bosh cck` to delete any placeholder VMs. If given the option, select **Delete VM reference**.

            $ bosh cck

              Acting as user 'director' on deployment 'cf-e82cbf44613594d8a155' on 'p-bosh-30c19bdd43c55c627d70'
            Performing cloud check...

            Director task 34
              Started scanning 22 vms
              Started scanning 22 vms > Checking VM states. Done (00:00:10)
              Started scanning 22 vms > 19 OK, 0 unresponsive, 3 missing, 0 unbound, 0 out of sync. Done (00:00:00)
                 Done scanning 22 vms (00:00:10)

              Started scanning 10 persistent disks
              Started scanning 10 persistent disks > Looking for inactive disks. Done (00:00:02)
              Started scanning 10 persistent disks > 10 OK, 0 missing, 0 inactive, 0 mount-info mismatch. Done (00:00:00)
                 Done scanning 10 persistent disks (00:00:02)

            Task 34 done

            Started   2015-11-26 01:42:42 UTC
            Finished  2015-11-26 01:42:54 UTC
            Duration  00:00:12

            Scan is complete, checking if any problems found.

            Found 3 problems

            Problem 1 of 3: VM with cloud ID `i-afe2801f' missing.
                1. Skip for now
                2. Recreate VM
                3. Delete VM reference
            Please choose a resolution [1 - 3]: 3

            Problem 2 of 3: VM with cloud ID `i-36741a86' missing.
                1. Skip for now
                2. Recreate VM
                3. Delete VM reference
            Please choose a resolution [1 - 3]: 3

            Problem 3 of 3: VM with cloud ID `i-ce751b7e' missing.
                1. Skip for now
                2. Recreate VM
                3. Delete VM reference
            Please choose a resolution [1 - 3]: 3

            Below is the list of resolutions you've provided
            Please make sure everything is fine and confirm your changes

                1. VM with cloud ID `i-afe2801' missing.
                 Delete VM reference

                2. VM with cloud ID `i-36741a86' missing.
                 Delete VM reference

                3. VM with cloud ID `i-ce751b7e' missing.
                 Delete VM reference

            Apply resolutions? (type 'yes' to continue): yes
            Applying resolutions...

            Director task 35
              Started applying problem resolutions
              Started applying problem resolutions > missing_vm 11: Delete VM reference. Done (00:00:00)
              Started applying problem resolutions > missing_vm 27: Delete VM reference. Done (00:00:00)
              Started applying problem resolutions > missing_vm 26: Delete VM reference. Done (00:00:00)
                 Done applying problem resolutions (00:00:00)

            Task 35 done

            Started   2015-11-26 01:47:08 UTC
            Finished  2015-11-26 01:47:08 UTC
            Duration  00:00:00
            Cloudcheck is finished

        By watching `bosh instances` you'll see the VMs transition from `unresponsive agent` to `starting`. Ultimately, two will appear as `failing`, this is OK.

            $ bosh instances
            [...]
            +--------------------------------------------------+----------+------------------------------------------------+------------+
            | mysql-partition-e97dae91e44681e0b543/0           | starting | mysql-partition-e97dae91e44681e0b543           | 10.0.16.60 |
            | mysql-partition-e97dae91e44681e0b543/1           | failing  | mysql-partition-e97dae91e44681e0b543           | 10.0.16.61 |
            | mysql-partition-e97dae91e44681e0b543/2           | failing  | mysql-partition-e97dae91e44681e0b543           | 10.0.16.62 |
            +--------------------------------------------------+----------+------------------------------------------------+------------+

        Do not proceed to the next step until all three VMs are in the `starting`/`failing` state.

        #### <a id="updating-manifest"></a>Update the BOSH configuration ####

        In standard deployment, BOSH is configured to manage the cluster in a specific manner. You must change that configuration in order for the bootstrap errand to perform its work. Follow this process to make it possible for the bootstrap errand to succeed.

        1. Log into the BOSH director
        1. Target the correct deployment
        1. `bosh edit deployment`
            * Search for the jobs section: `jobs`
            * Search for the mysql-partition: `mysql-partition`
            * Search for the update section: `update`
            * Change max\_in\_flight to `3`.
            * Below the `max_in_flight` line, add a line: `canaries: 0`
        1. `bosh deploy`

        #### <a id="run-the-errand"></a>Run the bootstrap errand ####
        1. `bosh run errand bootstrap`
        1. Validate that the errand completes successfully.
            - Some instances may still appear as `failing`. It's OK to proceed to the next step.

        #### <a id="reset-deployment"></a>Restore the BOSH configuration ####
        1. `bosh edit deployment`
        1. Re-set `canaries` to 1, `max_in_flight` to 1, and `serial` to true in the same manner as above.
        1. `bosh deploy`
        1. Validate that all mysql instances are in `running` state.

        <p class="note"><strong>Note:</strong> It is critical that you run all of the steps. If you do not re-set the values in the BOSH manifest, the status of the jobs will not be reported correctly and can lead to troubles in future deploys.</p>

---

## <a id="manual-bootstrap"></a>Manual Bootstrap ##

1. If the bootstrap errand is not able to automatically recover the cluster, you may need to perform the steps manually. The following steps are prone to user-error and can result in lost data if followed incorrectly. Please follow the [assisted boostrap](#assisted-bootstrap) instructions above first, and only resort to the manual process if the errand fails to repair the cluster.

    1. SSH to each node in the cluster and, as root, shut down the mariadb process.

            $ monit stop mariadb_ctrl

        Re-bootstrapping the cluster will not be successful unless all other nodes have been shut down.

    1. Choose a node to bootstrap.

        Find the node with the highest transaction sequence number (seqno). The sequence number of a stopped node can be retained by either reading the node's state file under `/var/vcap/store/mysql/grastate.dat`, or by running a mysqld command with a WSREP flag, like `mysqld --wsrep-recover`.<br><br>

        If a node shutdown gracefully, the seqno should be in the galera state file.

            $ cat /var/vcap/store/mysql/grastate.dat | grep 'seqno:'

        If the node crashed or was killed, the seqno in the galera state file should be `-1`. In this case, the seqno may be recoverable from the database. The following command will cause the database to start up, log the recovered sequence number, and then exit.

            $ /var/vcap/packages/mariadb/bin/mysqld --wsrep-recover

        **Note:** The galera state file will still say `seqno: -1` afterward.<br><br>

        Scan the error log for the recovered sequence number (the last number after the group id (uuid) is the recovered seqno):

            $ grep "Recovered position" /var/vcap/sys/log/mysql/mysql.err.log | tail -1
            150225 18:09:42 mysqld_safe WSREP: Recovered position e93955c7-b797-11e4-9faa-9a6f0b73eb46:15

        If the node never connected to the cluster before crashing, it may not even have a group id (uuid in grastate.dat). In this case there's nothing to recover. Unless all nodes crashed this way, don't choose this node for bootstrapping.

        
        #### <a id="set-bootstrap-node"></a>Bootstrap the first node ####
        
        Use the node with the highest `seqno` value as the new bootstrap node. If all nodes have the same `seqno`, you can choose any node as the new bootstrap node.


        <p class="note"><strong>Note:</strong> Only perform these bootstrap commands on the node with the highest seqno. Otherwise the node with the highest seqno will be unable to join the new cluster (unless its data is abandoned). Its mariadb process will exit with an error. See <a href="./cluster-behavior.html">cluster behavior</a> for more details on intentionally abandoning data.</p>

    1. On the new bootstrap node, update state file and restart the mariadb process:

            $ echo -n "NEEDS_BOOTSTRAP" > /var/vcap/store/mysql/state.txt
            $ monit start mariadb_ctrl

        You can check that the mariadb process has started successfully by running:

            $ watch monit summary

        It can take up to 10 minutes for monit to start the mariadb process.

        #### <a id="restart-nodes"></a>Restart the remaining nodes ####
        
    1. Once the bootstrapped node is running, start the mariadb process on the remaining nodes via monit.

            $ monit start mariadb_ctrl

    1. Verify that the new nodes have successfully joined the cluster. The following command should output the total number of nodes in the cluster:

            mysql> SHOW STATUS LIKE 'wsrep_cluster_size';

