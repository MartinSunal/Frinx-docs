
User Guide: Daexim
==================

Introduction
------------

FRINX daexim is a fork from ODL's daexim project. It contains several modifications aimed at improved import and export:

Important files
---------------

After daexim export, the following files and folders are created in the karaf folder:


* daexim

  * odl_backup_config.json - snapshot of config datastore
  * odl_backup_operational.json - snapshot of operational datastore

* etc/org.opendaylight.daexim.cfg - property file discussed below
* schemas/ - contains all yang files which were loaded in ODL when export was executed

Import
------

Originally, import was done anytime during runtime. The drawback of this approach is that applications can listen on changes in the datastore and start writing to it while import is still in progress.

Another drawback is that the entire import ran as a single transaction. This created pressure on the underlying communication layer, causing intermittent failures in akka.

FRINX daexim solves those problems by using many small transactions and triggering import during karaf startup. If import should be executed, the operator must set import flag in a property file on all nodes. When nodes form a cluster, they vote for one node to do the import.

Once import is done, karaf will continue booting other features. A consequence of running import before installing all bundles is that not all yang models are available. Thus, both import and export use the new folder:

.. code-block:: guess

   ${karaf.home}/schemas


where all yang files are extracted from karaf's system folder and loaded during karaf startup. Json files that contain actual data are stored and read from the following folder:

.. code-block:: guess

   ${karaf.home}/daexim


org.opendaylight.daexim.cfg
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The defaults that affect daexim are stored in the file etc/org.opendaylight.daexim.cfg. The defaults are:

.. code-block:: guess

   daexim.importOnInit=false
   daexim.importBatchSize=1500

Enabling automatic import during startup
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To enable automatic import, place the backed up json files into

.. code-block:: guess

   ${karaf.home}/daexim


,yang schemas into

.. code-block:: guess

   ${karaf.home}/schemas


and make sure org.opendaylight.daexim.cfg contains the following on all nodes:

.. code-block:: guess

   daexim.importOnInit=true


Karaf property files affected by the FRINX daexim changes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The changes mentioned here are already in place in the official FRINX distribution. However, if you are building your own karaf, ensure that your etc folder has no unintended deviations from FRINX karaf.

Because frinx daexim needs to start before all other ODL features, the featuresBoot specified in

.. code-block:: guess

   ${karaf.home}/etc/org.apache.karaf.features.cfg


must be split into two properties:

.. code-block:: guess

   (config,standard,region,package,kar,ssh,management,odl-jolokia),(odl-restconf),(odl-daexim-all)
   odlFeaturesBoot=odl-netconf-topology,customer-feature1



Property featuresBoot must only contain core features necessary for loading daexim, optionally with jolokia and restconf for troubleshooting. It is recommended not to modify this line. ODL features should be placed into the new odlFeaturesBoot property, where multiple features are delimited with the comma(,) sign.

Since karaf loads bundles of previously started features before loading featuresBoot, we must delete the following directory before every start:

.. code-block:: guess

   ${karaf.home}/data/cache


This can be automated by changing a line in the file

.. code-block:: guess

   ${karaf.home}/etc/system.properties


.. code-block:: guess

   karaf.clean.cache=true


Changing batch size
~~~~~~~~~~~~~~~~~~~

The Daexim initial import process reads the content of json files and sends it as transactions to data stores. However, executing +100 MB transactions is risky as it may affect the stability of the cluster. That is why import splits the changes into many transactions. The number of changes per transaction is controlled by the property ``daexim.importBatchSize``. The default value is 1500. Setting it too high may result in AskTimeoutExceptions and leader isolation failures. Setting it too low will make startup very slow. Unless there are problems with the default it is not advised to change this value.

Export
------

Daexim export was changed so that it is executed only on the node which was contacted via restconf:

.. code-block:: bash

   curl -u admin:admin  "ODL_NODE_1:8181/restconf/operations/data-export-import:simple-export" -X POST -H "Content-Type: application/json" -d '{"input": {}}' -v

In this case, the export will be executed on ODL_NODE_1. Note that the RPC is slightly different than what Daexim supports by default - simple-export does not need time and date to be supplied, export will start immediately. For advanced use, the operator can specify a list of excluded tuples: model,data store (config, operational). This behavior is the same as with ODL's daexim project.

Exporting from leader node
~~~~~~~~~~~~~~~~~~~~~~~~~~

Reading the whole datastore within a cluster can be slow and can cause pressure on the system leading to intermittent node failures. Therefore it is advised to run the export on the shard leader. This way all data will be read from local memory. To determine the node that contains the leaders of both shards (default-operational, default-config), call the following:

.. code-block:: bash

   curl -u admin:admin  "ODL_NODE_1:8181/jolokia/read/org.opendaylight.controller:Category=ShardManager,name=shard-manager-config,type=DistributedConfigDatastore
   curl -u admin:admin  "ODL_NODE_1:8181/jolokia/read/org.opendaylight.controller:Category=ShardManager,name=shard-manager-operational,type=DistributedOperationalDatastore

Example output:

.. code-block:: json

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

Note that leaderId points to the node containing the shard leader, attributes shardReady,shardReadyWithLeaderId,shardInitialized inform that cluster is stable.

Details about both shards can be obtained by calling:

.. code-block:: bash

   ID=1
   SHARD_NAME=default-operational
   TYPE=DistributedOperationalDatastore
   curl -u admin:admin  "ODL_NODE_1:8181/jolokia/read/org.opendaylight.controller:Category=Shards,name=member-${ID}-shard-${SHARD_NAME},type=${TYPE}
   SHARD_NAME=default-config
   TYPE=DistributedConfigDatastore
   curl -u admin:admin  "ODL_NODE_1:8181/jolokia/read/org.opendaylight.controller:Category=Shards,name=member-${ID}-shard-${SHARD_NAME},type=${TYPE}

General info on daexim
----------------------

`OpenDaylight Wiki page on daexim <https://wiki.opendaylight.org/view/Daexim:Main>`__
Data Export/Import (daexim) is a project introduced in the OpenDaylight Carbon release. However, daexim has been back ported to FRINX distributions and is available from Beryllium 1.4.6 and Boron 2.3.0 and subsequent releases.

The purpose of the project is to export/import data from files. Here are the key functions of the project:


* Export of CONF and OPER DS
* Export files in JSON format
* Component is controlled via RPC API `see here <https://www.youtube.com/watch?v=fCWuuS-_xy4>`__
* Data can be excluded from export based on yang module and datastore type
* Datastore can be cleared before data is imported
* Export can be scheduled

`Video tutorial with Postman collection <https://www.youtube.com/watch?v=fCWuuS-_xy4>`__  

:download:`Postman collection <daexim_postman.json>`

Export data from datastore
~~~~~~~~~~~~~~~~~~~~~~~~~~

RPC result is returned immediately after a task for export is scheduled. State of export can be shown via the RPC API.

When RPC schedule-export is invoked, the scheduled export is stored to OPER DS. Therefore, the scheduled export is replicated on other nodes in a cluster deployment.

DataExportImportAppProvider, on each cluster node, receives a modification event about the scheduled export and schedules ExportTask which executes datastore export. Therefore, RPC for schedule export can be invoked on any cluster node and datastore is exported on each cluster node.

Import data to datastore
~~~~~~~~~~~~~~~~~~~~~~~~

Data import is done on startup when daexim.importOnInit is set to true. Data is imported from JSON files to CONF and OPER datastore in one transaction. In a cluster deployment, data import is executed only on a cluster node where RPC is invoked and data is replicated to other nodes within the transaction.
