# Developer Guide: Daexim

<!-- TOC START min:1 max:3 link:true update:true -->
- [Developer Guide: Daexim](#developer-guide-daexim)
  - [Overview](#overview)
  - [Import](#import)
  - [Export](#export)
    - [Exporting from leader node](#exporting-from-leader-node)
  - [General info on daexim](#general-info-on-daexim)

<!-- TOC END -->

**daexim - datastore export import**

## Overview

Frinx daexim is a fork from ODL's daexim project. It contains several modifications aimed at improved import and export:

## Import

Originally, import was done anytime during runtime. The drawback of this approach is that applications can listen on changes in the datastore and start writing to it while import is still in progress.

Another drawback is that the entire import ran as a single transaction. This created pressure on the underlying communication layer, causing intermittent failures in akka.

Frinx daexim solves those problems by using many small transactions and triggering import during karaf startup. If import should be executed, the operator must set import flag in a property file on all nodes. When nodes form a cluster, they vote for one node to do the import.

Once import is done, karaf will continue booting other features. A consequence of running import before installing all bundles is that not all yang models are available. Thus, both import and export use the new folder:

> ${karaf.home}/schemas

where all yang files are backed up (during export) and loaded (during karaf startup). Json files that contain actual data are stored and read from the following folder:

> ${karaf.home}/daexim

**Enabling automatic import during startup**

To enable automatic import, place the backed up json files into

> ${karaf.home}/daexim

, yang schemas to

> ${karaf.home}/schemas

and execute the following on all nodes:

    echo 'daexim.importOnInit=true' > ${KARAF_HOME}/etc/org.opendaylight.daexim.cfg


**Karaf property files affected by the Frinx daexim changes**

The changes mentioned here are already in place in the official Frinx distribution. However, if you are building your own karaf, ensure that your etc folder has no unintended deviations from Frinx karaf.

Because frinx daexim needs to start before all other ODL features, the featuresBoot specified in

> ${karaf.home}/etc/org.apache.karaf.features.cfg

must be split into two properties:

    (config,standard,region,package,kar,ssh,management,odl-jolokia),(odl-restconf),(odl-daexim-all)
    odlFeaturesBoot=odl-netconf-topology,customer-feature1


Property featuresBoot must only contain core features necessary for loading daexim, optionally with jolokia and restconf for troubleshooting. It is recommended not to modify this line. ODL features should be placed into the new odlFeaturesBoot property, where multiple features are delimited with the comma(,) sign.

Since karaf loads bundles of previously started features before loading featuresBoot, we must delete the following directory before every start:

> ${karaf.home}/data/cache

This can be automated by changing a line in the file

> ${karaf.home}/etc/system.properties

    karaf.clean.cache=true


## Export

Daexim export was changed so that it is executed only on the node which was contacted via restconf:

    curl -u admin:admin  "ODL_NODE_1:8181/restconf/operations/data-export-import:simple-export" -X POST -H "Content-Type: application/json" -d '{"input": {}}' -v


In this case, the export will be executed on ODL_NODE_1. Note that the RPC is slightly different than what Daexim supports by default - simple-export does not need time and date to be supplied, export will start immediately. For advanced use, operator can specify list of excluded tuples: model,data store (config, operational). This behavior is the same as with ODL's daexim project.

### Exporting from leader node

Reading the whole datastore within a cluster can be slow and can cause pressure on the system leading to intermittent node failures. Therefore it is advised to run the export on shard leader. This way all data will be read from local memory. To determine the node that contains leaders of both shards (default-operational, default-config), call the following:

    curl -u admin:admin  "ODL_NODE_1:8181/jolokia/read/org.opendaylight.controller:Category=ShardManager,name=shard-manager-config,type=DistributedConfigDatastore
    curl -u admin:admin  "ODL_NODE_1:8181/jolokia/read/org.opendaylight.controller:Category=ShardManager,name=shard-manager-operational,type=DistributedOperationalDatastore


Example output:

    {
        "request": {
            "mbean": "org.opendaylight.controller:Category=ShardManager,name=shard-manager-operational,type=DistributedOperationalDatastore",
            "type": "read"
        },
        "status": 200,
        "timestamp": 1506949569,
        "value": {
            "LocalShardInformation": {
                "member-1-shard-default-operational": {
                    "activeMember": true,
                    "inSync": true,
                    "leader": false,
                    "leaderId": "member-3-shard-default-operational",
                    "leaderVersion": 3,
                    "shardId": {
                        "memberName": "member-1",
                        "shardName": "default",
                        "type": "operational"
                    },
                    "shardInitialized": true,
                    "shardName": "default",
                    "shardReady": true,
                    "shardReadyWithLeaderId": true
                }
            },
            "LocalShards": [
                "member-1-shard-default-operational"
            ],
            "MemberName": "member-1",
            "SyncStatus": true
        }
    }


Note that leaderId points to the node containing shard leader, attributes shardReady,shardReadyWithLeaderId,shardInitialized inform that cluster is stable.

Details about each of both shards can be obtained by calling

    ID=1
    SHARD_NAME=default-operational
    TYPE=DistributedOperationalDatastore
    curl -u admin:admin  "ODL_NODE_1:8181/jolokia/read/org.opendaylight.controller:Category=Shards,name=member-${ID}-shard-${SHARD_NAME},type=${TYPE}
    SHARD_NAME=default-config
    TYPE=DistributedConfigDatastore
    curl -u admin:admin  "ODL_NODE_1:8181/jolokia/read/org.opendaylight.controller:Category=Shards,name=member-${ID}-shard-${SHARD_NAME},type=${TYPE}


## General info on daexim

[OpenDaylight Wiki page on daexim][1]  
Data Export/Import (daexim) is a project introduced in the OpenDaylight Carbon release. However, daexim has been back ported to FRINX distributions and is available from Beryllium 1.4.6 and Boron 2.3.0 and subsequent releases.

The purpose of the project is to export/import data from files. Here are the key functions of the project:

*   Export of CONF and OPER DS
*   Export files in JSON format
*   Component is controlled via RPC API [see here][2]
*   Data can be excluded from export based on yang module and datastore type
*   Datastore can be cleared before data is imported
*   Export can be scheduled

[Video tutorial with Postman collection][2]

**Export data from datastore**

RPC result is returned immediately after a task for export is scheduled. State of export can be shown via the RPC API.

When RPC schedule-export is invoked, the scheduled export is stored to OPER DS. Therefore, the scheduled export is replicated on other nodes in a cluster deployment.

DataExportImportAppProvider, on each cluster node, receives a modification event about the scheduled export and schedules ExportTask which executes datastore export. Therefore, RPC for schedule export can be invoked on any cluster node and datastore is exported on each cluster node.

**Import data to datastore**  
Data import is done by calling RPC immediate-import. Data is imported from JSON files to CONF and OPER datastore in one transaction. In a cluster deployment, data import is executed only on a cluster node where RPC is invoked and data is replicated to other nodes within the transaction.

 [1]: https://wiki.opendaylight.org/view/Daexim:Main
 [2]: https://www.youtube.com/watch?v=fCWuuS-_xy4
