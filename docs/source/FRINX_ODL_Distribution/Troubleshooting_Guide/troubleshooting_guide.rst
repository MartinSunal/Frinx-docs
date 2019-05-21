
Troubleshooting Guide
---------------------

The troubleshooting guide is based around a `flow chart <http://i.imgur.com/2fwTem4.png>`__

This guide can be used in two ways:

**Method 1: I have a log message and want to know what it means**

In the flow chart, log excerpts are colored orange and placed towards the base of the flow chart. Search this text guide for "Log excerpt" text, then find the "Log excerpt" of the same number on the flow chart and navigate upwards until you come to a node which describes your situation. You will then see the suggested cause and actions.

For example: If the log error is "SEVERE: FRINX License fatal error" then this maps to Log Excerpt 1 on the flow chart. Moving upwards from log excerpt 1 you arrive at "log contains SEVERE:FRINX license fatal error". We see that we take the path "Yes" and get to the next step: investigate if etc/frinx.license.cfg exists.

NOTE: log excerpts 16-23 are generated during tests, not during normal operations.

**Method 2: The FRINX controller is not behaving as expected and I want to troubleshoot to establish the problem**

In this case you should start at the very top of the flow chart and navigate through it using the decision boxes. As you navigate the chart you will be guided on what to check and on what measures to take to identify and resolve the problem.

For example: If the API is not available and we are not sure if the API ports are listening on the ODL host, then the suggested action is to verify the firewall is not blocking.

Table of content:

*  `Log Excerpt 1 <#log-excerpt-1>`__
*  `Log Excerpt 2 <#log-excerpt-2>`__
*  `Log Excerpt 3 <#log-excerpt-3>`__
*  `Log Excerpt 4 <#log-excerpt-4>`__
*  `Log Excerpt 5 <#log-excerpt-5>`__
*  `Log Excerpt 6 <#log-excerpt-6>`__
*  `Log Excerpt 7 <#log-excerpt-7>`__
*  `Log Excerpt 8 <#log-excerpt-8>`__
*  `Log Excerpt 9 <#log-excerpt-9>`__
*  `Log Excerpt 10 <#log-excerpt-10>`__
*  `Log Excerpt 11 <#log-excerpt-11>`__
*  `Log Excerpt 12 <#log-excerpt-12>`__
*  `Log Excerpt 13 <#log-excerpt-13>`__
*  `Log Excerpt 14 <#log-excerpt-14>`__
*  `Log Excerpt 15 <#log-excerpt-15>`__
*  `Log Excerpt 16 <#log-excerpt-16>`__
*  `Log Excerpt 17 <#log-excerpt-17>`__
*  `Log Excerpt 18 <#log-excerpt-18>`__
*  `Log Excerpt 19 <#log-excerpt-19>`__
*  `Log Excerpt 20 <#log-excerpt-20>`__
*  `Log Excerpt 21 <#log-excerpt-21>`__
*  `Log Excerpt 22 <#log-excerpt-22>`__
*  `Log Excerpt 23 <#log-excerpt-23>`__
*  `Log Excerpt 24 <#log-excerpt-24>`__

Log Excerpt 1:
~~~~~~~~~~~~~~

**Log Message:**
SEVERE: FRINX License fatal error:
`see flow chart` <http://i.imgur.com/2fwTem4.png>`__

**Description of the problem:**
The FRINX license is not applied to the FRINX controller.

**Test Name:**

.. code-block:: text

   csit/suites/licenseserver/license_sanity.robot

**Test Execution:**

.. code-block:: text

   pybot --variable ODL_SYSTEM_IP:$i    --variable USER_HOME:releng/sshkeysforlab    --variable KARAF_INSTALLDIR:${KARAF_INSTALLDIR} --variable ODL_SYSTEM_USER:vagrant --variable ODL_SYSTEM_PROMPT:'$' --variable BUNDLEFOLDER:${BUNDLEFOLDER} --variable TIMEOUT:60  --variable REFRESH:5 ./csit/suites/licenseserver/license_sanity.robot`

**Objective of test:**

Generates a new token, uses the token to generate a license and verifies it is added.

**Correction Steps:**

Contact support@frinx.io

Log Excerpt 2:
~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   java.lang.OutOfMemory.Error

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the problem:**
FRINX controller does not have large enough Java heap available.

**Test Name:**
There are no automated tests but this error can be simulated by trying to start the controller with 124m available.

**Test Execution:**
Provision a VM and set the controller start-up to use a small heap. Edit the bin/karaf file by increasing the heap space, the -Xmx setting,

**Objective of test:**
Verify the FRINX ODL controller will start.

**Correction Steps:**
For normal use the recommended resources are:

* HW/SW configuration:
* Use dedicated VM or bare-metal for controller
* CPU: 4 Cores
* RAM: 6 GB
* OS: Linux Server (no Desktop) is recommended. Report used OS when publishing results.

Log Excerpt 3:
~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 04:28:23,308 | INFO | Event Dispatcher | FeaturesServiceImpl | 20 - org.apache.karaf.features.core - 3.0.3.13-frinxkaraf | Installing feature odl-config-all 0.4.4-Beryllium-SR4.4-frinxodl

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the problem:**

The FRINX controller is installing a feature.

Log Excerpt 4:
~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 04:28:18,531 | DEBUG | Event Dispatcher | FeaturesServiceImpl | 20 - org.apache.karaf.features.core - 3.0.3.13-frinxkaraf | Feature odl-yangtools-common with version 0.8.4-Beryllium-SR4.4-frinxodl is already being installed

**Description of the problem:**

The FRINX controller is already installing a feature.

Log Excerpt 5:
~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   listening on addresses :[akka.tcp://opendaylight-cluster-data@10.10.199.121:2550] 2016-12-19 04:29:08,497 | INFO | ult-dispatcher-6 | kka://opendaylight-cluster-data) | 141 - com.typesafe.akka.slf4j - 2.3.14 | Cluster Node [akka.tcp://opendaylight-cluster-data@10.10.199.121:2550] - Starting up...

**Description of the problem:**

The FRINX controller clustering process has opened a local port as defined in akka.conf and is listening for a connection.

**Test Name:**

Cluster deployment generates this error.

**Correction Steps:**

No action is normally needed; the error stops being generated once the remote member of the cluster is connected.

Log Excerpt 6:
~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 04:29:09,220 | INFO | ult-dispatcher-7 | rovider$RemoteDeadLetterActorRef | 141 - com.typesafe.akka.slf4j - 2.3.14 | Message [akka.cluster.InternalClusterAction$InitJoin$] from Actor[akka://opendaylight-cluster-data/system/cluster/core/daemon/firstSeedNodeProcess-1###1308405232] to Actor[akka://opendaylight-cluster-data/deadLetters] was not delivered. [1] dead letters encountered. This logging can be turned off or adjusted with configuration settings 'akka.log-dead-letters' and 'akka.log-dead-letters-during-shutdown'.

   2016-12-19 04:29:09,226 | WARN | ult-dispatcher-7 | ReliableDeliverySupervisor | 141 - com.typesafe.akka.slf4j - 2.3.14 | Association with remote system [akka.tcp://opendaylight-cluster-data@10.10.199.150:2550] has failed, address is now gated for [5000] ms. Reason: [Association failed with [akka.tcp://opendaylight-cluster-data@10.10.199.150:2550]] Caused by: [Connection refused: /10.10.199.150:2550]

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the problem:**
The FRINX controller clustering process is trying to open a connection with a remote cluster member which is not available because the remote port is not open.

Cluster deployment generates this error during start-up.

**Correction Steps:**
No action is normally needed; the error stops being generated when the remote member of the cluster is connected. If this error persists then clustering configuration and network layer connectivity should be verified.

Log Excerpt 7:
~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 04:29:14,773 | WARN | saction-40-42'}} | DeadlockMonitor | 103 - org.opendaylight.controller.config-manager - 0.4.4.Beryllium-SR4_4-frinxodl | ModuleIdentifier{factoryName='cluster-admin-provider', instanceName='cluster-admin-provider'} did not finish after 9976 ms

   2016-12-19 04:29:14,778 | WARN | ult-dispatcher-3 | ReliableDeliverySupervisor | 141 - com.typesafe.akka.slf4j - 2.3.14 | Association with remote system [akka.tcp://opendaylight-cluster-data@10.10.199.202:2550] has failed, address is now gated for [5000] ms. Reason: [Association failed with [akka.tcp://opendaylight-cluster-data@10.10.199.202:2550]] Caused by: [Connection refused: /10.10.199.202:2550]

   2016-12-19 04:29:14,778 | WARN | ult-dispatcher-3 | ReliableDeliverySupervisor | 141 - com.typesafe.akka.slf4j - 2.3.14 | Association with remote system [akka.tcp://opendaylight-cluster-data@10.10.199.150:2550] has failed, address is now gated for [5000] ms. Reason: [Association failed with [akka.tcp://opendaylight-cluster-data@10.10.199.150:2550]] Caused by: [Connection refused: /10.10.199.150:2550] **Description of the problem: **

`See flowchart <http://i.imgur.com/2fwTem4.png`__

**Description of the problem:**
The FRINX controller clustering process is trying to open a connection with a remote cluster member which is not available because the remote port is not open.

**Test Name:**
Cluster deployment generates this error during start-up.

**Correction Steps:**
No action is normally needed; the error stops being generated when the remote member of the cluster is connected. If this error persists then clustering configuration and network layer connectivity should be verified.

Log Excerpt 8:
~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 04:29:59,235 | INFO | ult-dispatcher-3 | kka://opendaylight-cluster-data) | 141 - com.typesafe.akka.slf4j - 2.3.14 | Cluster Node [akka.tcp://opendaylight-cluster-data@10.10.199.121:2550] - Node [akka.tcp://opendaylight-cluster-data@10.10.199.202:2550] is JOINING, roles [member-2]

   2016-12-19 04:29:59,716 | INFO | ult-dispatcher-2 | kka://opendaylight-cluster-data) | 141 - com.typesafe.akka.slf4j - 2.3.14 | Cluster Node [akka.tcp://opendaylight-cluster-data@10.10.199.121:2550] - Leader is moving node [akka.tcp://opendaylight-cluster-data@10.10.199.202:2550] to [Up]

   2016-12-19 04:29:59,717 | DEBUG | ult-dispatcher-2 | ShardManager | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | shard-manager-operational: Received MemberUp: memberName: member-2, address: akka.tcp://opendaylight-cluster-data@10.10.199.202:2550 

**Description of the problem:**

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the problem:**
The FRINX controller clustering process on member-1 has successfully opened a connection with a remote cluster member-2.

**Correction Steps:**
No action is usually needed the message is generated when the remote member of the cluster is connected. If this error persists then clustering configuration and network layer connectivity should be verified.

Log Excerpt 9:
~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 04:30:00,461 | DEBUG | ult-dispatcher-4 | Shard | 152 - org.opendaylight.controller.sal-akka-raft - 1.3.4.Beryllium-SR4_4-frinxodl | member-1-shard-default-operational (Leader): Election: Leader has following peers: [member-3-shard-default-operational, member-2-shard-default-operational]

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the problem:**
The FRINX controller clustering process on member-1 has successfully completed and a three member cluster is deployed with a remote cluster consisting of member-1, member-2, member-3

**Correction Steps:**
No action is normally needed; the message is generated when the remote member of the cluster is connected. If this error persists then clustering configuration and network layer connectivity should be verified.

Log Excerpt 10:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 04:29:09,704 | INFO | lt-dispatcher-18 | Shard | 152 - org.opendaylight.controller.sal-akka-raft - 1.3.4.Beryllium-SR4_4-frinxodl | Shard created : member-1-shard-default-operational, persistent : false

   2016-12-19 04:29:09,710 | INFO | ult-dispatcher-7 | Shard | 152 - org.opendaylight.controller.sal-akka-raft - 1.3.4.Beryllium-SR4_4-frinxodl | Shard created : member-1-shard-toaster-operational, persistent : false

   2016-12-19 04:29:09,712 | INFO | lt-dispatcher-17 | Shard | 152 - org.opendaylight.controller.sal-akka-raft - 1.3.4.Beryllium-SR4_4-frinxodl | Shard created : member-1-shard-inventory-operational, persistent : false

   2016-12-19 04:29:09,712 | INFO | ult-dispatcher-5 | Shard | 152 - org.opendaylight.controller.sal-akka-raft - 1.3.4.Beryllium-SR4_4-frinxodl | Shard created : member-1-shard-topology-operational, persistent : false

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the situation:**
The FRINX controller clustering process on member-1 has successfully created local shards called 'default-operational', 'toaster-operational', 'inventory-operational', 'topology-operational'

**Correction Steps:**
No action is usually needed the message is generated when the controller starts up. If it is not seen then clustering configuration should be verified.

Log Excerpt 11:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 04:30:00,462 | INFO | ult-dispatcher-4 | ShardManager | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | shard-manager-operational: All Shards are ready - data store operational is ready, available count is 1

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the situation:**
The FRINX controller clustering process on member-1 has successfully created local shards and registered them in the operational datastore.

**Test Name:**
There is no test specifically for this message; cluster deployment generates this log message during start-up of the 3-node cluster.

**Correction Steps:**
No action is usually needed the message is generated when the controller starts up. If it is not seen then clustering configuration should be verified.

Log Excerpt 12:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 04:30:10,745 | INFO | ult-dispatcher-2 | ShardManager | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | shard-manager-config: All Shards are ready - data store config is ready, available count is 1

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the situation:**
The FRINX controller clustering process on member-1 has successfully created local shards and registered them in the configurational datastore.

**Correction Steps:**
The message is generated when the controller starts up. If it is not seen then clustering configuration should be verified.

Log Excerpt 13:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-11-08 08:02:46,399 | WARN | ult-dispatcher-5 | ClusterCoreDaemon | 148 - com.typesafe.akka.slf4j - 2.3.14 | Cluster Node [akka.tcp://opendaylight-cluster-data@10.10.199.243:2550] - Marking node(s) as UNREACHABLE [Member(address = akka.tcp://opendaylight-cluster-data@10.10.199.220:2550, status = Up)]

   2016-11-08 08:02:46,400 | DEBUG | ult-dispatcher-5 | ShardManager | 162 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_2-frinxodl-SNAPSHOT | Received UnreachableMember: memberName member-3, address: akka.tcp://opendaylight-cluster-data@10.10.199.220:2550

   2016-11-08 08:02:46,400 | DEBUG | ult-dispatcher-5 | ShardManager | 162 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_2-frinxodl-SNAPSHOT | Marking Leader member-3-shard-default-operational as unavailable.

   2016-11-08 08:02:46,400 | DEBUG | ult-dispatcher-5 | ShardManager | 162 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_2-frinxodl-SNAPSHOT | Received UnreachableMember: memberName member-3, address: akka.tcp://opendaylight-cluster-data@10.10.199.220:2550

   2016-11-08 08:02:46,400 | INFO | ult-dispatcher-5 | EntityOwnershipShard | 159 - org.opendaylight.controller.sal-akka-raft - 1.3.4.Beryllium-SR4_2-frinxodl-SNAPSHOT | member-1-shard-entity-ownership-operational: onPeerDown: PeerDown [memberName=member-3, peerId=member-3-shard-entity-ownership-operational]

   2016-11-08 08:02:46,473 | WARN | ult-dispatcher-4 | ReliableDeliverySupervisor | 148 - com.typesafe.akka.slf4j - 2.3.14 | Association with remote system [akka.tcp://opendaylight-cluster-data@10.10.199.220:2550] has failed, address is now gated for [5000] ms. Reason: [Association failed with [akka.tcp://opendaylight-cluster-data@10.10.199.220:2550]] Caused by: [Connection refused: /10.10.199.220:2550]

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the situation:**
The FRINX controller on member-1 has detected that the member-3 has become unavailable.

**Test Name:**
This message is generated in many isolation tests and network errors.

csit/suites/netconf/single_node/netopeer.robot

**Test Execution:**

.. code-block:: text

       pybot -x junit.xml --removekeywords wuks  -v \
       BUNDLEFOLDER:${BUNDLEFOLDER} -v WORKSPACE:${WORKSPACE} -v BUNDLE_URL: -v \
       JAVA_HOME:${JAVA_HOME} -v NEXUSURL_PREFIX: -v JDKVERSION:${JDKVERSION} -v \
       ODL_STREAM:${ODL_STREAM} -v CONTROLLER:${ODL_SYSTEM_1_IP} -v \
       CONTROLLER1:${ODL_SYSTEM_2_IP} -v CONTROLLER2:${ODL_SYSTEM_3_IP} -v \
       ODL_SYSTEM_IP:${ODL_SYSTEM_1_IP} -v ODL_SYSTEM_1_IP:${ODL_SYSTEM_1_IP} -v \
       ODL_SYSTEM_2_IP:${ODL_SYSTEM_2_IP} -v ODL_SYSTEM_3_IP:${ODL_SYSTEM_3_IP} -v \
       NUM_ODL_SYSTEM:${NUM_ODL_SYSTEM} -v CONTROLLER_USER:${CONTROLLER_USER} -v \
       ODL_SYSTEM_USER:${ODL_SYSTEM_USER} -v TOOLS_SYSTEM_IP:${TOOLS_SYSTEM_IP} -v \
       TOOLS_SYSTEM_1_IP:${TOOLS_SYSTEM_IP} -v NUM_TOOLS_SYSTEM:${NUM_TOOLS_SYSTEM} \
       -v TOOLS_SYSTEM_USER:${TOOLS_SYSTEM_USER} -v MININET:${TOOLS_SYSTEM_IP} -v MININET1: \
       -v MININET2: -v MININET_USER:${MININET_USER} -v USER_HOME:${USER_HOME} -v \
       ODL_SYSTEM_PROMPT:'$' -v USE_NETCONF_CONNECTOR:${USE_NETCONF_CONNECTOR} -v  \
       BUNDLE_URL:${BUNDLE_URL} -v ODL_HOST:${ODL_SYSTEM_1_IP} -v ODL_HOST_2:${ODL_SYSTEM_2_IP} -v  ODL_HOST_3:${ODL_SYSTEM_3_IP} -v \
       TOOLS_SYSTEM_PROMPT:'$' -v \
       BINARY_REPOSITORY_USER:${BINARY_REPOSITORY_USER} -v \
       BINARY_REPOSITORY_PASSWORD:${BINARY_REPOSITORY_PASSWORD} \
       csit/suites/netconf/single_node/netopeer.robot

**Objective of test:**
Verify that a FRINX controller 3 member cluster can recover from an outage of one node.

**Correction Steps:**
If a member is down, in this example it is member-3 at '10.10.199.220' then use the troubleshooting chart to investigate (start at the top).

Log Excerpt 14:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-11-08 08:03:30,525 | INFO | lt-dispatcher-17 | kka://opendaylight-cluster-data) | 148 - com.typesafe.akka.slf4j - 2.3.14 | Cluster Node [akka.tcp://opendaylight-cluster-data@10.10.199.243:2550] - Node [akka.tcp://opendaylight-cluster-data@10.10.199.220:2550] is JOINING, roles [member-3]

   2016-11-08 08:03:31,110 | INFO | ult-dispatcher-2 | Shard | 159 - org.opendaylight.controller.sal-akka-raft - 1.3.4.Beryllium-SR4_2-frinxodl-SNAPSHOT | member-1-shard-default-operational: Initiating snapshot capture CaptureSnapshot [lastAppliedIndex=2, lastAppliedTerm=1, lastIndex=2, lastTerm=1, installSnapshotInitiated=true, replicatedToAllIndex=-1, replicatedToAllTerm=-1, unAppliedEntries size=0] to install on member-3-shard-default-operational

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the situation:**

The FRINX controller on member-1 has detected that the member-3 has become available after an outage.

**Test Name:**

This message is generated in many isolation tests and network errors. The tests suites are located in the folder:

csit/suites/netconf/clustering

**Test Execution:**

.. code-block:: text

   BUNDLEFOLDER:${BUNDLEFOLDER} -v WORKSPACE:${WORKSPACE} -v BUNDLE_URL: -v JAVA_HOME:${JAVA_HOME} -v NEXUSURL_PREFIX: -v JDKVERSION:${JDKVERSION} -v ODL_STREAM:${ODL_STREAM} -v CONTROLLER:${ODL_SYSTEM_1_IP} -v CONTROLLER1:${ODL_SYSTEM_2_IP} -v CONTROLLER2:${ODL_SYSTEM_3_IP} -v ODL_SYSTEM_IP:${ODL_SYSTEM_1_IP} -v ODL_SYSTEM_1_IP:${ODL_SYSTEM_1_IP} -v ODL_SYSTEM_2_IP:${ODL_SYSTEM_2_IP} -v ODL_SYSTEM_3_IP:${ODL_SYSTEM_3_IP} -v NUM_ODL_SYSTEM:${NUM_ODL_SYSTEM} -v CONTROLLER_USER:${CONTROLLER_USER} -v ODL_SYSTEM_USER:${ODL_SYSTEM_USER} -v TOOLS_SYSTEM_IP:${TOOLS_SYSTEM_IP} -v TOOLS_SYSTEM_1_IP:${TOOLS_SYSTEM_IP} -v NUM_TOOLS_SYSTEM:${NUM_TOOLS_SYSTEM} -v TOOLS_SYSTEM_USER:${TOOLS_SYSTEM_USER} -v MININET:${TOOLS_SYSTEM_IP} -v MININET1: -v MININET2: -v MININET_USER:${MININET_USER} -v USER_HOME:${USER_HOME} -v ODL_SYSTEM_PROMPT:'$' -v USE_NETCONF_CONNECTOR:${USE_NETCONF_CONNECTOR} -v BUNDLE_URL:${BUNDLE_URL} -v ODL_HOST:${ODL_SYSTEM_1_IP} -v ODL_HOST_2:${ODL_SYSTEM_2_IP} -v ODL_HOST_3:${ODL_SYSTEM_3_IP} -v TOOLS_SYSTEM_PROMPT:'$' -v BINARY_REPOSITORY_USER:${BINARY_REPOSITORY_USER} -v BINARY_REPOSITORY_PASSWORD:${BINARY_REPOSITORY_PASSWORD} csit/suites/netconf/clustering/netopeer_for_cluster.robot

**Objective of test:**
Verify that a netconf connection between a netopeer device a FRINX ODL controller 3 member cluster can recover from an outage of one node.

**Correction Steps:**
If a member was down and has recovered, investigate the system logs of the machine hosting OLD to confirm that the outage was intended.

Log Excerpt 15:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-20 13:39:25,377 | DEBUG | on-dispatcher-44 | taTreeNotificationPublisherActor | 151 - org.opendaylight.controller.sal-clustering-commons - 1.3.4.Beryllium-SR4_4-frinxodl | member-2-shard-car-config: Elapsed time for generation of change events for member-2-shard-car-config-DCL-publisher: 180.1 μs

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the situation:**
Test suites are run to verify cluster operation. The files akka.conf, modules.conf and modules-shards.conf must be updated first.

**Test Names:**
/csit/suites/controller/Clustering_Datastore/cluster_ready.robot
/csit/suites/controller/Clustering_Datastore/car_failover_crud.robot

**Test Execution:**

.. code-block:: text

       pybot --removekeywords wuks -c critical -v BUNDLEFOLDER:distribution-karaf-1.4.1.frinx -v WORKSPACE:/tmp/frinx -v BUNDLE_URL: -v JAVA_HOME:/opt/jdk1.8.0_65 -v NEXUSURL_PREFIX: -v JDKVERSION:1.8.0_65 -v ODL_STREAM:beryllium -v CONTROLLER:10.10.199.245 -v CONTROLLER1:10.10.199.131 -v CONTROLLER2:10.10.199.21 -v ODL_SYSTEM_IP:10.10.199.245 -v ODL_SYSTEM_1_IP:10.10.199.245 -v ODL_SYSTEM_2_IP:10.10.199.131 -v ODL_SYSTEM_3_IP:10.10.199.21 -v NUM_ODL_SYSTEM:3 -v CONTROLLER_USER:vagrant -v ODL_SYSTEM_USER:vagrant -v TOOLS_SYSTEM_IP:10.10.199.38 -v TOOLS_SYSTEM_1_IP:10.10.199.38 -v NUM_TOOLS_SYSTEM:1 -v TOOLS_SYSTEM_USER:vagrant -v MININET:10.10.199.38 -v MININET1: -v MININET2: -v MININET_USER: -v USER_HOME:releng/sshkeysforlab/ -v USE_NETCONF_CONNECTOR:True -v 'ODL_SYSTEM_PROMPT:$' -v BUNDLE_URL:https://artifactory.frinx.io/artifactory/frinx-releases/org/opendaylight/ -v UPSTREAM_ODL_VERSION:1.0.3-Beryllium-SR3.1-frinxodl -v 'TOOLS_SYSTEM_PROMPT:$' -v BINARY_REPOSITORY_USER:robotuser -v BINARY_REPOSITORY_PASSWORD:f04lyLJsHnWMw1Yb91G2 -v NEXUS_FALLBACK_URL:HTTPS://ARTIFACTORY.FRINX.IO/ARTIFACTORY/FRINX-SNAPSHOTS/ORG/OPENDAYLIGHT/ -v USER_HOME:releng/sshkeysforlab/ --xunit xunit.xml csit/suites/controller/Clustering_Datastore/car_failover_crud.robot

**Objective of test:**
Verifies that the cluster is ready to run further tests. Suite mixing basic operations with restart of car Leader. This test kills the current leader of the "car" shard and then executes CRD operations on the new leader and a new follower. The killed member is brought back.

**Correction Steps:**
Failure of these test suites indicates that clustering functionality is impaired. Verify the test setup and test environment are correctly configured then raise a bug report.

Log Excerpt 16:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-20 13:54:12,963 | DEBUG | qtp1600092363-62 | TransactionProxy | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | Tx member-1-txn-3-1482260052963 DataExists /(urn:opendaylight:params:xml:ns:yang:controller:config:sal-clustering-it:car?revision=2014-08-18)cars

   2016-12-20 13:54:12,963 | DEBUG | qtp1600092363-62 | bstractTransactionContextFactory | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | Tx member-1-txn-3-1482260052963: Found primary ActorSelection[Anchor(akka://opendaylight-cluster-data/), Path(/user/shardmanager-config/member-1-shard-car-config###1945916102)] for shard car

   2016-12-20 13:54:12,963 | DEBUG | qtp1600092363-62 | bstractTransactionContextFactory | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | Tx member-1-txn-3-1482260052963 - Creating local component for shard car using factory org.opendaylight.controller.cluster.datastore.LocalTransactionFactoryImpl@4019a911

   2016-12-20 13:54:12,965 | DEBUG | qtp1600092363-62 | TransactionProxy | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | Tx member-1-txn-3-1482260052963 executeModification DeleteModification /(urn:opendaylight:params:xml:ns:yang:controller:config:sal-clustering-it:car?revision=2014-08-18)cars

   2016-12-20 13:54:12,965 | DEBUG | qtp1600092363-62 | TransactionProxy | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | Tx member-1-txn-3-1482260052963 Readying 1 components for commit

   2016-12-20 13:54:12,965 | DEBUG | qtp1600092363-62 | TransactionProxy | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | Tx member-1-txn-3-1482260052963 Readying transaction for shard car

   2016-12-20 13:54:12,965 | DEBUG | qtp1600092363-62 | AbstractTransactionContext | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | Total modifications on Tx member-1-txn-3-1482260052963 = [ 1 ]

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the situation:**
Test suites are run to verify cluster operation. The files akka.conf, modules.conf and modules-shards.conf must be updated first.

**Test Names:**
/csit/suites/controller/Clustering_Datastore/cluster_ready.robot
/csit/suites/controller/Clustering_Datastore/car_persistence_recovery.robot

**Test Execution:**

.. code-block:: text

       pybot --removekeywords wuks -c critical -v BUNDLEFOLDER:distribution-karaf-1.4.1.frinx -v WORKSPACE:/tmp/frinx -v BUNDLE_URL: -v JAVA_HOME:/opt/jdk1.8.0_65 -v NEXUSURL_PREFIX: -v JDKVERSION:1.8.0_65 -v ODL_STREAM:beryllium -v CONTROLLER:10.10.199.245 -v CONTROLLER1:10.10.199.131 -v CONTROLLER2:10.10.199.21 -v ODL_SYSTEM_IP:10.10.199.245 -v ODL_SYSTEM_1_IP:10.10.199.245 -v ODL_SYSTEM_2_IP:10.10.199.131 -v ODL_SYSTEM_3_IP:10.10.199.21 -v NUM_ODL_SYSTEM:3 -v CONTROLLER_USER:vagrant -v ODL_SYSTEM_USER:vagrant -v TOOLS_SYSTEM_IP:10.10.199.38 -v TOOLS_SYSTEM_1_IP:10.10.199.38 -v NUM_TOOLS_SYSTEM:1 -v TOOLS_SYSTEM_USER:vagrant -v MININET:10.10.199.38 -v MININET1: -v MININET2: -v MININET_USER: -v USER_HOME:releng/sshkeysforlab/ -v USE_NETCONF_CONNECTOR:True -v 'ODL_SYSTEM_PROMPT:$' -v BUNDLE_URL:https://artifactory.frinx.io/artifactory/frinx-releases/org/opendaylight/ -v UPSTREAM_ODL_VERSION:1.0.3-Beryllium-SR3.1-frinxodl -v 'TOOLS_SYSTEM_PROMPT:$' -v BINARY_REPOSITORY_USER:robotuser -v BINARY_REPOSITORY_PASSWORD:f04lyLJsHnWMw1Yb91G2 -v NEXUS_FALLBACK_URL:HTTPS://ARTIFACTORY.FRINX.IO/ARTIFACTORY/FRINX-SNAPSHOTS/ORG/OPENDAYLIGHT/ -v USER_HOME:releng/sshkeysforlab/ --xunit xunit.xml csit/suites/controller/Clustering_Datastore/car_persistence_recovery.robot

**Objective of test:**
Verifies that the cluster is ready to run further tests. This test restarts all controllers to verify recovery of car data from persistence

**Correction Steps:**
Failure of these test suites indicates that clustering functionality is impaired. Verify the test setup and test environment are correctly configured then raise a bug report.

Log Excerpt 17:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-20 14:05:34,646 | DEBUG | ult-dispatcher-5 | Shard | 152 - org.opendaylight.controller.sal-akka-raft - 1.3.4.Beryllium-SR4_4-frinxodl | member-1-shard-topology-config (Leader): handleAppendEntriesReply from member-2-shard-topology-config: applying to log - commitIndex: 13, lastAppliedIndex: 12

   2016-12-20 14:05:34,646 | DEBUG | ult-dispatcher-5 | Shard | 152 - org.opendaylight.controller.sal-akka-raft - 1.3.4.Beryllium-SR4_4-frinxodl | member-1-shard-topology-config (Leader): Setting last applied to 13

   2016-12-20 14:05:34,646 | DEBUG | ult-dispatcher-5 | Shard | 152 - org.opendaylight.controller.sal-akka-raft - 1.3.4.Beryllium-SR4_4-frinxodl | member-1-shard-topology-config: fakeSnapshot purging log to 12 for term 4

   2016-12-20 14:05:34,646 | DEBUG | ult-dispatcher-5 | Shard | 152 - org.opendaylight.controller.sal-akka-raft - 1.3.4.Beryllium-SR4_4-frinxodl | member-1-shard-topology-config (Leader): Checking sendAppendEntries for follower member-2-shard-topology-config: active: true, followerNextIndex: 14, leaderLastIndex: 13, leaderSnapShotIndex: 12

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the situation:**
Test suites are run to verify cluster operation. The files akka.conf, modules.conf and modules-shards.conf must be updated first.

**Test Names:**
/csit/suites/controller/Clustering_Datastore/cluster_ready.robot
/csit/suites/controller/Clustering_Datastore/car_outage_corners.robot

**Test Execution:**

.. code-block:: text

       pybot --removekeywords wuks -c critical -v BUNDLEFOLDER:distribution-karaf-1.4.1.frinx -v WORKSPACE:/tmp/frinx -v BUNDLE_URL: -v JAVA_HOME:/opt/jdk1.8.0_65 -v NEXUSURL_PREFIX: -v JDKVERSION:1.8.0_65 -v ODL_STREAM:beryllium -v CONTROLLER:10.10.199.245 -v CONTROLLER1:10.10.199.131 -v CONTROLLER2:10.10.199.21 -v ODL_SYSTEM_IP:10.10.199.245 -v ODL_SYSTEM_1_IP:10.10.199.245 -v ODL_SYSTEM_2_IP:10.10.199.131 -v ODL_SYSTEM_3_IP:10.10.199.21 -v NUM_ODL_SYSTEM:3 -v CONTROLLER_USER:vagrant -v ODL_SYSTEM_USER:vagrant -v TOOLS_SYSTEM_IP:10.10.199.38 -v TOOLS_SYSTEM_1_IP:10.10.199.38 -v NUM_TOOLS_SYSTEM:1 -v TOOLS_SYSTEM_USER:vagrant -v MININET:10.10.199.38 -v MININET1: -v MININET2: -v MININET_USER: -v USER_HOME:releng/sshkeysforlab/ -v USE_NETCONF_CONNECTOR:True -v 'ODL_SYSTEM_PROMPT:$' -v BUNDLE_URL:https://artifactory.frinx.io/artifactory/frinx-releases/org/opendaylight/ -v UPSTREAM_ODL_VERSION:1.0.3-Beryllium-SR3.1-frinxodl -v 'TOOLS_SYSTEM_PROMPT:$' -v BINARY_REPOSITORY_USER:robotuser -v BINARY_REPOSITORY_PASSWORD:f04lyLJsHnWMw1Yb91G2 -v NEXUS_FALLBACK_URL:HTTPS://ARTIFACTORY.FRINX.IO/ARTIFACTORY/FRINX-SNAPSHOTS/ORG/OPENDAYLIGHT/ -v USER_HOME:releng/sshkeysforlab/ --xunit xunit.xml csit/suites/controller/Clustering_Datastore/car_outage_corners.robot

**Objective of test:**
Cluster suite for testing minimal and sum-minimal member population behavior. This test kills majority of the followers and verifies car addition is not possible, then resumes single follower (first from original list) and checks that addition works. Then remaining members are brought up. Leader member is always up and assumed to remain Leading during the whole suite run.

**Correction Steps:**
Failure of these test suites indicates that clustering functionality is impaired. Verify the test setup and test environment are correctly configured then raise a bug report.

Log Excerpt 18:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-20 14:14:50,960 | DEBUG | on-dispatcher-44 | taTreeNotificationPublisherActor | 151 - org.opendaylight.controller.sal-clustering-commons - 1.3.4.Beryllium-SR4_4-frinxodl | Actor created Actor[akka://opendaylight-cluster-data/user/shardmanager-config/member-1-shard-car-config/member-1-shard-car-config-DCL-publisher###1237366541]

   2016-12-20 14:14:50,960 | DEBUG | on-dispatcher-44 | taTreeNotificationPublisherActor | 151 - org.opendaylight.controller.sal-clustering-commons - 1.3.4.Beryllium-SR4_4-frinxodl | member-1-shard-car-config: Elapsed time for generation of change events for member-1-shard-car-config-DCL-publisher: 40.37 μs

   2016-12-20 14:15:11,260 | DEBUG | ternal.Finalizer | RemoteTransactionContext | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | Tx member-1-txn-3-1482261279118 closeTransaction called

   2016-12-20 14:15:11,260 | DEBUG | ternal.Finalizer | ActorContext | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | Sending message class org.opendaylight.controller.protobuff.messages.transaction.ShardTransactionMessages$CloseTransaction to ActorSelection[Anchor(akka.tcp://opendaylight-cluster-data@10.10.199.21:2550/), Path(/user/shardmanager-config/member-3-shard-car-people-config/shard-member-1-txn-3-1482261279118###86474125)]

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the situation:**

Test suites are run to verify cluster operation. The files akka.conf, modules.conf and modules-shards.conf must be updated first.

**Test Names:**

/csit/suites/controller/Clustering_Datastore/cluster_ready.robot
/csit/suites/controller/Clustering_Datastore/car_outage_corners.robot

**Test Execution:**

.. code-block:: text

       pybot --removekeywords wuks -c critical -v BUNDLEFOLDER:distribution-karaf-1.4.1.frinx -v WORKSPACE:/tmp/frinx -v BUNDLE_URL: -v JAVA_HOME:/opt/jdk1.8.0_65 -v NEXUSURL_PREFIX: -v JDKVERSION:1.8.0_65 -v ODL_STREAM:beryllium -v CONTROLLER:10.10.199.245 -v CONTROLLER1:10.10.199.131 -v CONTROLLER2:10.10.199.21 -v ODL_SYSTEM_IP:10.10.199.245 -v ODL_SYSTEM_1_IP:10.10.199.245 -v ODL_SYSTEM_2_IP:10.10.199.131 -v ODL_SYSTEM_3_IP:10.10.199.21 -v NUM_ODL_SYSTEM:3 -v CONTROLLER_USER:vagrant -v ODL_SYSTEM_USER:vagrant -v TOOLS_SYSTEM_IP:10.10.199.38 -v TOOLS_SYSTEM_1_IP:10.10.199.38 -v NUM_TOOLS_SYSTEM:1 -v TOOLS_SYSTEM_USER:vagrant -v MININET:10.10.199.38 -v MININET1: -v MININET2: -v MININET_USER: -v USER_HOME:releng/sshkeysforlab/ -v USE_NETCONF_CONNECTOR:True -v 'ODL_SYSTEM_PROMPT:$' -v BUNDLE_URL:https://artifactory.frinx.io/artifactory/frinx-releases/org/opendaylight/ -v UPSTREAM_ODL_VERSION:1.0.3-Beryllium-SR3.1-frinxodl -v 'TOOLS_SYSTEM_PROMPT:$' -v BINARY_REPOSITORY_USER:robotuser -v BINARY_REPOSITORY_PASSWORD:f04lyLJsHnWMw1Yb91G2 -v NEXUS_FALLBACK_URL:HTTPS://ARTIFACTORY.FRINX.IO/ARTIFACTORY/FRINX-SNAPSHOTS/ORG/OPENDAYLIGHT/ -v USER_HOME:releng/sshkeysforlab/ --xunit xunit.xml csit/suites/controller/Clustering_Datastore/buycar_failover.robot

**Objective of test:**

This test focuses on testing buy-car RPC over 3 Leader reboots. All purchases are against the same node, which is the first one to get rebooted. All data is deleted at the end of the suite. This suite expects car, people and car-people modules to have separate Shards.

**Correction Steps:**

Failure of these test suites indicates that clustering functionality is impaired. Verify the test setup and test environment are correctly configured then raise a bug report.

Log Excerpt 19:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-20 14:23:21,875 | INFO | ult-dispatcher-4 | ShardManager | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | shard-manager-operational: Received LeaderStateChanged message: LeaderStateChanged [memberId=member-1-shard-people-operational, leaderId=member-3-shard-people-operational, leaderPayloadVersion=3] 2016-12-20 14:23:21,875 | INFO | ult-dispatcher-4 | ShardManager | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | shard-manager-operational: All Shards are ready - data store operational is ready, available count is 0 2016-12-20 14:23:21,875 | INFO | ult-dispatcher-4 | ShardManager | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | shard-manager-operational Received follower initial sync status for member-1-shard-people-operational status sync done false

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the situation:**
Test suites are run to verify basic cluster operation without depending on an overlaying framework such as Netconf topology, or BGP. The files akka.conf, modules.conf and modules-shards.conf must be updated first.

**Test Names:**
/csit/suites/controller/Clustering_Datastore/cluster_ready.robot
/csit/suites/controller/Clustering_Datastore/entity_isolate.robot

**Test Execution:**

.. code-block:: text

       pybot --removekeywords wuks -c critical -v BUNDLEFOLDER:distribution-karaf-1.4.1.frinx -v WORKSPACE:/tmp/frinx -v BUNDLE_URL: -v JAVA_HOME:/opt/jdk1.8.0_65 -v NEXUSURL_PREFIX: -v JDKVERSION:1.8.0_65 -v ODL_STREAM:beryllium -v CONTROLLER:10.10.199.245 -v CONTROLLER1:10.10.199.131 -v CONTROLLER2:10.10.199.21 -v ODL_SYSTEM_IP:10.10.199.245 -v ODL_SYSTEM_1_IP:10.10.199.245 -v ODL_SYSTEM_2_IP:10.10.199.131 -v ODL_SYSTEM_3_IP:10.10.199.21 -v NUM_ODL_SYSTEM:3 -v CONTROLLER_USER:vagrant -v ODL_SYSTEM_USER:vagrant -v TOOLS_SYSTEM_IP:10.10.199.38 -v TOOLS_SYSTEM_1_IP:10.10.199.38 -v NUM_TOOLS_SYSTEM:1 -v TOOLS_SYSTEM_USER:vagrant -v MININET:10.10.199.38 -v MININET1: -v MININET2: -v MININET_USER: -v USER_HOME:releng/sshkeysforlab/ -v USE_NETCONF_CONNECTOR:True -v 'ODL_SYSTEM_PROMPT:$' -v BUNDLE_URL:https://artifactory.frinx.io/artifactory/frinx-releases/org/opendaylight/ -v UPSTREAM_ODL_VERSION:1.0.3-Beryllium-SR3.1-frinxodl -v 'TOOLS_SYSTEM_PROMPT:$' -v BINARY_REPOSITORY_USER:robotuser -v BINARY_REPOSITORY_PASSWORD:f04lyLJsHnWMw1Yb91G2 -v NEXUS_FALLBACK_URL:HTTPS://ARTIFACTORY.FRINX.IO/ARTIFACTORY/FRINX-SNAPSHOTS/ORG/OPENDAYLIGHT/ -v USER_HOME:releng/sshkeysforlab/ --xunit xunit.xml csit/suites/controller/Clustering_Datastore/entity_isolate.robot

**Objective of test:**
This test focuses on testing member isolation and rejoin, using entity-ownership leader

**Correction Steps:**
Failure of these test suites indicates that clustering functionality is impaired. Verify the test setup and test environment are correctly configured then raise a bug report.

Log Excerpt 20:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 07:15:19,492 | DEBUG | sing-executor-14 | NetconfDevice | 283 - org.opendaylight.netconf.sal-netconf-connector - 1.3.4.Beryllium-SR4_4-frinxodl | RemoteDevice{netopeer}: Unable to map any source identifiers to a capability reported by device : [] 2016-12-19 07:15:19,617 | DEBUG | sing-executor-14 | NetconfDevice | 283 - org.opendaylight.netconf.sal-netconf-connector - 1.3.4.Beryllium-SR4_4-frinxodl | RemoteDevice{netopeer}: Schema context built successfully from [SourceIdentifier [name=ietf-netconf@2011-03-08], SourceIdentifier [name=ietf-inet-types@2010-09-24], SourceIdentifier [name=ietf-netconf-monitoring@2010-10-04], SourceIdentifier [name=toaster@2009-11-20], SourceIdentifier [name=nc-notifications@2008-07-14], SourceIdentifier [name=ietf-yang-types@2010-09-24], SourceIdentifier [name=ietf-netconf-notifications@2011-08-07], SourceIdentifier [name=ietf-netconf-with-defaults@2010-06-09], SourceIdentifier [name=ietf-netconf-acm@2012-02-22], SourceIdentifier [name=notifications@2008-07-14]] 2016-12-19 07:15:19,621 | ERROR | sing-executor-14 | Futures$ImmediateFuture | 49 - com.google.guava - 18.0.0 | RuntimeException while executing runnable com.google.common.util.concurrent.Futures$6@6e03c639 with executor INSTANCE java.lang.IllegalStateException: RemoteDevice{netopeer}: Mount instance was not initialized by sal. Cannot get mount instance at com.google.common.base.Preconditions.checkState(Preconditions.java:197)[49:com.google.guava:18.0.0] at org.opendaylight.netconf.sal.connect.netconf.sal.NetconfDeviceSalProvider.getMountInstance(NetconfDeviceSalProvider.java:48)[283:org.opendaylight.netconf.sal-netconf-connector:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.netconf.sal.connect.netconf.sal.NetconfDeviceSalFacade.onDeviceConnected(NetconfDeviceSalFacade.java:65)[283:org.opendaylight.netconf.sal-netconf-connector:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.netconf.sal.connect.netconf.sal.NetconfDeviceSalFacade.onDeviceConnected(NetconfDeviceSalFacade.java:28)[283:org.opendaylight.netconf.sal-netconf-connector:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.netconf.sal.connect.netconf.NetconfDevice.handleSalInitializationSuccess(NetconfDevice.java:221)[283:org.opendaylight.netconf.sal-netconf-connector:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.netconf.sal.connect.netconf.NetconfDevice$RecursiveSchemaSetup$2.onSuccess(NetconfDevice.java:463)[283:org.opendaylight.netconf.sal-netconf-connector:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.netconf.sal.connect.netconf.NetconfDevice$RecursiveSchemaSetup$2.onSuccess(NetconfDevice.java:455)[283:org.opendaylight.netconf.sal-netconf-connector:1.3.4.Beryllium-SR4_4-frinxodl] at com.google.common.util.concurrent.Futures$6.run(Futures.java:1319)[49:com.google.guava:18.0.0] at com.google.common.util.concurrent.MoreExecutors$DirectExecutor.execute(MoreExecutors.java:457)[49:com.google.guava:18.0.0] at com.google.common.util.concurrent.Futures$ImmediateFuture.addListener(Futures.java:106)[49:com.google.guava:18.0.0] at com.google.common.util.concurrent.Futures.addCallback(Futures.java:1322)[49:com.google.guava:18.0.0] at com.google.common.util.concurrent.Futures.addCallback(Futures.java:1258)[49:com.google.guava:18.0.0] at org.opendaylight.netconf.sal.connect.netconf.NetconfDevice$RecursiveSchemaSetup.setUpSchema(NetconfDevice.java:495)[283:org.opendaylight.netconf.sal-netconf-connector:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.netconf.sal.connect.netconf.NetconfDevice$RecursiveSchemaSetup.run(NetconfDevice.java:417)[283:org.opendaylight.netconf.sal-netconf-connector:1.3.4.Beryllium-SR4_4-frinxodl] at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511)[:1.8.0_65] at java.util.concurrent.FutureTask.run(FutureTask.java:266)[:1.8.0_65] at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)[:1.8.0_65] at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)[:1.8.0_65] at java.lang.Thread.run(Thread.java:745)[:1.8.0_65]

`See flowchart<http://i.imgur.com/2fwTem4.png">`__

**Description of the situation:**
Test suites are run to verify ODL controller is able to mount netconf. This log message is produced when two processes are trying to write to the same node.

**Test Names:**
csit/suites/netconf/ready/netconfready.robot\
csit/suites/netconf/clustering/isolate_1member_netopeer.robot

**Test Execution:**

.. code-block:: text

       pybot -N netconf-clustering.txt --removekeywords wuks -c critical -e exclude -v BUNDLEFOLDER:distribution-karaf-0.4.5-SNAPSHOT -v WORKSPACE:/tmp -v BUNDLE_URL:https://nexus.opendaylight.org/content/repositories/opendaylight.snapshot/org/opendaylight/integration/distribution-karaf/0.4.5-SNAPSHOT/distribution-karaf-0.4.5-20161219.002820-405.zip -v JAVA_HOME:/usr/lib/jvm/java-1.7.0 -v NEXUSURL_PREFIX:https://nexus.opendaylight.org -v JDKVERSION:openjdk7 -v ODL_STREAM:beryllium -v CONTROLLER:10.29.12.125 -v CONTROLLER1:10.29.12.137 -v CONTROLLER2:10.29.12.73 -v ODL_SYSTEM_IP:10.29.12.125 -v ODL_SYSTEM_1_IP:10.29.12.125 -v ODL_SYSTEM_2_IP:10.29.12.137 -v ODL_SYSTEM_3_IP:10.29.12.73 -v NUM_ODL_SYSTEM:3 -v CONTROLLER_USER:jenkins -v ODL_SYSTEM_USER:jenkins -v TOOLS_SYSTEM_IP:10.29.13.47 -v TOOLS_SYSTEM_1_IP:10.29.13.47 -v NUM_TOOLS_SYSTEM:1 -v TOOLS_SYSTEM_USER:jenkins -v MININET:10.29.13.47 -v MININET1: -v MININET2: -v MININET_USER:jenkins -v USER_HOME:/home/jenkins -v USE_NETCONF_CONNECTOR:False csit/suites/netconf/clustering/isolate_1member_netopeer.robot

**Objective of test:**
This test focuses on testing Netconf end to end functionality with a netconf device. Netconf capability must be shared by both devices. The aim of the test is to mount a netopeer device by a cluster, bring down one member in the cluster, try some operations and then bring the member back up and verify that the cluster can reconnect.

**Correction Steps:**
Failure of these test suites indicates that clustering functionality is impaired. Verify the test setup and test environment are correctly configured then raise a bug report. Functionality can sometimes be restored by deleting the snapshot, journal and cache files.

Log Excerpt 21:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 07:15:19,186 | DEBUG | on-dispatcher-86 | DataTreeChangeListenerActor | 155 - org.opendaylight.controller.sal-distributed-datastore - 1.3.4.Beryllium-SR4_4-frinxodl | Sending change notification [DefaultDataTreeCandidate{rootPath=/(urn:TBD:params:xml:ns:yang:network-topology?revision=2013-10-21)network-topology/topology/topology[{(urn:TBD:params:xml:ns:yang:network-topology?revision=2013-10-21)topology-id=topology-netconf}]/node/node[{(urn:TBD:params:xml:ns:yang:network-topology?revision=2013-10-21)node-id=netopeer}], rootNode=ChildNode{mod = NodeModification [identifier=(urn:TBD:params:xml:ns:yang:network-topology?revision=2013-10-21)node[{(urn:TBD:params:xml:ns:yang:network-topology?revision=2013-10-21)node-id=netopeer}], modificationType=DELETE, childModification={}], oldMeta = SimpleContainerNode{version=org.opendaylight.yangtools.yang.data.api.schema.tree.spi.Version@2b3aeeef, data=ImmutableMapEntryNode{nodeIdentifier=(urn:TBD:params:xml:ns:yang:network-topology?revision=2013-10-21)node[{(urn:TBD:params:xml:ns:yang:network-topology?revision=2013-10-21)node-id=netopeer}], value=[ImmutableLeafNode{nodeIdentifier=(urn:TBD:params:xml:ns:yang:network-topology?revision=2013-10-21)node-id, value=netopeer, attributes={}}, ImmutableAugmentationNode{nodeIdentifier=AugmentationIdentifier{childNames=[(urn:opendaylight:netconf-node-topology?revision=2015-01-14)max-connection-attempts, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)keepalive-delay, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)credentials, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)between-attempts-timeout-millis, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)connection-timeout-millis, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)unavailable-capabilities, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)tcp-only, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)customization-factory, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)schema-cache-directory, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)sleep-factor, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)yang-module-capabilities, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)reconnect-on-changed-schema, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)host, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)default-request-timeout-millis, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)clustered-connection-status, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)pass-through, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)connected-message, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)available-capabilities, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)connection-status, (urn:opendaylight:netconf-node-topology?revision=2015-01-14)port]}, value=[ImmutableLeafNode{nodeIdentifier=(urn:opendaylight:netconf-node-topology?revision=2015-01-14)tcp-only, value=false, attributes={}}, ImmutableLeafNode{nodeIdentifier=(urn:opendaylight:netconf-node-topology?revision=2015-01-14)keepalive-delay, value=0, attributes={}}, ImmutableChoiceNode{nodeIdentifier=(urn:opendaylight:netconf-node-topology?revision=2015-01-14)credentials, value=[ImmutableLeafNode{nodeIdentifier=(urn:opendaylight:netconf-node-topology?revision=2015-01-14)password, value=root, attributes={}}, ImmutableLeafNode{nodeIdentifier=(urn:opendaylight:netconf-node-topology?revision=2015-01-14)username, value=root, attributes={}}]}, ImmutableLeafNode{nodeIdentifier=(urn:opendaylight:netconf-node-topology?revision=2015-01-14)host, value=10.10.199.38, attributes={}}, ImmutableLeafNode{nodeIdentifier=(urn:opendaylight:netconf-node-topology?revision=2015-01-14)port, value=1831, attributes={}}]}], attributes={}}}, newMeta = null}}] to listener org.opendaylight.controller.md.sal.binding.impl.BindingClusteredDOMDataTreeChangeListenerAdapter@5a2e5c73

   2016-12-19 07:15:19,191 | WARN | on-dispatcher-86 | NetconfDeviceCommunicator | 283 - org.opendaylight.netconf.sal-netconf-connector - 1.3.4.Beryllium-SR4_4-frinxodl | RemoteDevice{netopeer}: Session terminated Session closed

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the situation:**
Test suites are run to verify ODL controller cluster is able to perform CRUD operation on a Netconf device. This log shows a successful delete operation.

**Test Names:**
csit/suites/netconf/ready/netconfready.robot
csit/suites/netconf/clustering/CRUD.robot

**Test Execution:**

.. code-block:: text

       pybot -N netconf-clustering.txt --removekeywords wuks -c critical -e exclude -v BUNDLEFOLDER:distribution-karaf-0.4.5-SNAPSHOT -v WORKSPACE:/tmp -v BUNDLE_URL:https://nexus.opendaylight.org/content/repositories/opendaylight.snapshot/org/opendaylight/integration/distribution-karaf/0.4.5-SNAPSHOT/distribution-karaf-0.4.5-20161219.002820-405.zip -v JAVA_HOME:/usr/lib/jvm/java-1.7.0 -v NEXUSURL_PREFIX:https://nexus.opendaylight.org -v JDKVERSION:openjdk7 -v ODL_STREAM:beryllium -v CONTROLLER:10.29.12.125 -v CONTROLLER1:10.29.12.137 -v CONTROLLER2:10.29.12.73 -v ODL_SYSTEM_IP:10.29.12.125 -v ODL_SYSTEM_1_IP:10.29.12.125 -v ODL_SYSTEM_2_IP:10.29.12.137 -v ODL_SYSTEM_3_IP:10.29.12.73 -v NUM_ODL_SYSTEM:3 -v CONTROLLER_USER:jenkins -v ODL_SYSTEM_USER:jenkins -v TOOLS_SYSTEM_IP:10.29.13.47 -v TOOLS_SYSTEM_1_IP:10.29.13.47 -v NUM_TOOLS_SYSTEM:1 -v TOOLS_SYSTEM_USER:jenkins -v MININET:10.29.13.47 -v MININET1: -v MININET2: -v MININET_USER:jenkins -v USER_HOME:/home/jenkins -v USE_NETCONF_CONNECTOR:False csit/suites/netconf/clustering/CRUD.robot

**Objective of test:**
This test focuses on testing netconf end to end functionality with a netconf device. A Netconf device must be removable from the topology.

**Correction Steps:**
Failure of these test suites indicates that clustering functionality is impaired. Verify the test setup and test environment are correctly configured then raise a bug report.

Log Excerpt 22:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 06:21:48,318 | DEBUG | lt-dispatcher-17 | Shard | 152 - org.opendaylight.controller.sal-akka-raft - 1.3.4.Beryllium-SR4_4-frinxodl | member-1-shard-topology-operational: An exception occurred during canCommit java.util.concurrent.ExecutionException: OptimisticLockFailedException{message=Optimistic lock failed., errorList=[RpcError [message=Optimistic lock failed., severity=ERROR, errorType=APPLICATION, tag=resource-denied, applicationTag=null, info=null, cause=org.opendaylight.yangtools.yang.data.api.schema.tree.ConflictingModificationAppliedException: Node was deleted by other transaction.]]} at com.google.common.util.concurrent.Futures$ImmediateFailedFuture.get(Futures.java:190)[49:com.google.guava:18.0.0] at org.opendaylight.controller.cluster.datastore.ShardCommitCoordinator$CohortEntry.canCommit(ShardCommitCoordinator.java:662)[155:org.opendaylight.controller.sal-distributed-datastore:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.controller.cluster.datastore.ShardCommitCoordinator.doCanCommit(ShardCommitCoordinator.java:330)[155:org.opendaylight.controller.sal-distributed-datastore:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.controller.cluster.datastore.ShardCommitCoordinator.maybeProcessNextCohortEntry(ShardCommitCoordinator.java:557)[155:org.opendaylight.controller.sal-distributed-datastore:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.controller.cluster.datastore.ShardCommitCoordinator.currentTransactionComplete(ShardCommitCoordinator.java:538)[155:org.opendaylight.controller.sal-distributed-datastore:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.controller.cluster.datastore.Shard.finishCommit(Shard.java:384)[155:org.opendaylight.controller.sal-distributed-datastore:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.controller.cluster.datastore.Shard.finishCommit(Shard.java:420)[155:org.opendaylight.controller.sal-distributed-datastore:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.controller.cluster.datastore.Shard.applyState(Shard.java:668)[155:org.opendaylight.controller.sal-distributed-datastore:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.controller.cluster.raft.RaftActor.handleCommand(RaftActor.java:225)[152:org.opendaylight.controller.sal-akka-raft:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.controller.cluster.common.actor.AbstractUntypedPersistentActor.onReceiveCommand(AbstractUntypedPersistentActor.java:36)[151:org.opendaylight.controller.sal-clustering-commons:1.3.4.Beryllium-SR4_4-frinxodl] at org.opendaylight.controller.cluster.datastore.Shard.onReceiveCommand(Shard.java:276)[155:org.opendaylight.controller.sal-distributed-datastore:1.3.4.Beryllium-SR4_4-frinxodl] at akka.persistence.UntypedPersistentActor.onReceive(Eventsourced.scala:430)[145:com.typesafe.akka.persistence.experimental:2.3.14] at org.opendaylight.controller.cluster.common.actor.MeteringBehavior.apply(MeteringBehavior.java:97)[151:org.opendaylight.controller.sal-clustering-commons:1.3.4.Beryllium-SR4_4-frinxodl] at akka.actor.ActorCell$$anonfun$become$1.applyOrElse(ActorCell.scala:534)[140:com.typesafe.akka.actor:2.3.14] at akka.persistence.Recovery$State$class.process(Recovery.scala:30)[145:com.typesafe.akka.persistence.experimental:2.3.14] at akka.persistence.ProcessorImpl$$anon$2.process(Processor.scala:103)[145:com.typesafe.akka.persistence.experimental:2.3.14] at akka.persistence.ProcessorImpl$$anon$2.aroundReceive(Processor.scala:114)[145:com.typesafe.akka.persistence.experimental:2.3.14] at akka.persistence.Recovery$class.aroundReceive(Recovery.scala:265)[145:com.typesafe.akka.persistence.experimental:2.3.14] at akka.persistence.UntypedPersistentActor.akka$persistence$Eventsourced$$super$aroundReceive(Eventsourced.scala:428)[145:com.typesafe.akka.persistence.experimental:2.3.14] at akka.persistence.Eventsourced$$anon$2.doAroundReceive(Eventsourced.scala:82)[145:com.typesafe.akka.persistence.experimental:2.3.14] at akka.persistence.Eventsourced$$anon$2.aroundReceive(Eventsourced.scala:78)[145:com.typesafe.akka.persistence.experimental:2.3.14] at akka.persistence.Eventsourced$class.aroundReceive(Eventsourced.scala:369)[145:com.typesafe.akka.persistence.experimental:2.3.14] at akka.persistence.UntypedPersistentActor.aroundReceive(Eventsourced.scala:428)[145:com.typesafe.akka.persistence.experimental:2.3.14] at akka.actor.ActorCell.receiveMessage(ActorCell.scala:516)[140:com.typesafe.akka.actor:2.3.14] at akka.actor.ActorCell.invoke(ActorCell.scala:487)[140:com.typesafe.akka.actor:2.3.14] at akka.dispatch.Mailbox.processMailbox(Mailbox.scala:238)[140:com.typesafe.akka.actor:2.3.14] at akka.dispatch.Mailbox.run(Mailbox.scala:220)[140:com.typesafe.akka.actor:2.3.14] at akka.dispatch.ForkJoinExecutorConfigurator$AkkaForkJoinTask.exec(AbstractDispatcher.scala:397)[140:com.typesafe.akka.actor:2.3.14] at scala.concurrent.forkjoin.ForkJoinTask.doExec(ForkJoinTask.java:260)[137:org.scala-lang.scala-library:2.11.7.v20150622-112736-1fbce4612c] at scala.concurrent.forkjoin.ForkJoinPool$WorkQueue.runTask(ForkJoinPool.java:1339)[137:org.scala-lang.scala-library:2.11.7.v20150622-112736-1fbce4612c] at scala.concurrent.forkjoin.ForkJoinPool.runWorker(ForkJoinPool.java:1979)[137:org.scala-lang.scala-library:2.11.7.v20150622-112736-1fbce4612c] at scala.concurrent.forkjoin.ForkJoinWorkerThread.run(ForkJoinWorkerThread.java:107)[137:org.scala-lang.scala-library:2.11.7.v20150622-112736-1fbce4612c] Caused by: OptimisticLockFailedException{message=Optimistic lock failed., errorList=[RpcError [message=Optimistic lock failed., severity=ERROR, errorType=APPLICATION, tag=resource-denied, applicationTag=null, info=null, cause=org.opendaylight.yangtools.yang.data.api.schema.tree.ConflictingModificationAppliedException: Node was deleted by other transaction.]]}

See flowchart<http://i.imgur.com/2fwTem4.png>`__

**Description of the situation:**
Test suites are run to verify ODL controller is unable to delete a Netconf device because the device has already been deleted.

**Test Names:**  


* csit/suites/netconf/ready/netconfready.robot

* csit/suites/netconf/clustering/CRUD.robot

**Test Execution:**

.. code-block:: text

       pybot -N netconf-clustering.txt --removekeywords wuks -c critical -e exclude -v BUNDLEFOLDER:distribution-karaf-0.4.5-SNAPSHOT -v WORKSPACE:/tmp -v BUNDLE_URL:https://nexus.opendaylight.org/content/repositories/opendaylight.snapshot/org/opendaylight/integration/distribution-karaf/0.4.5-SNAPSHOT/distribution-karaf-0.4.5-20161219.002820-405.zip -v JAVA_HOME:/usr/lib/jvm/java-1.7.0 -v NEXUSURL_PREFIX:https://nexus.opendaylight.org -v JDKVERSION:openjdk7 -v ODL_STREAM:beryllium -v CONTROLLER:10.29.12.125 -v CONTROLLER1:10.29.12.137 -v CONTROLLER2:10.29.12.73 -v ODL_SYSTEM_IP:10.29.12.125 -v ODL_SYSTEM_1_IP:10.29.12.125 -v ODL_SYSTEM_2_IP:10.29.12.137 -v ODL_SYSTEM_3_IP:10.29.12.73 -v NUM_ODL_SYSTEM:3 -v CONTROLLER_USER:jenkins -v ODL_SYSTEM_USER:jenkins -v TOOLS_SYSTEM_IP:10.29.13.47 -v TOOLS_SYSTEM_1_IP:10.29.13.47 -v NUM_TOOLS_SYSTEM:1 -v TOOLS_SYSTEM_USER:jenkins -v MININET:10.29.13.47 -v MININET1: -v MININET2: -v MININET_USER:jenkins -v USER_HOME:/home/jenkins -v USE_NETCONF_CONNECTOR:False csit/suites/netconf/clustering/CRUD.robot

**Objective of test:**
This test focuses on testing Netconf end to end functionality with a netconf device. A Netconf device must be removable from the topology. If it has already been removed then this error message should appear.

**Correction Steps:**
Failure of these test suites indicates that clustering functionality is impaired. Verify the test setup and test environment are correctly configured then raise a bug report.

Log Excerpt 23:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 06:04:21,421 | WARN | ocketConnector-1 | AsyncSshHandler | 268 - org.opendaylight.netconf.netty-util - 1.0.4.Beryllium-SR4_4-frinxodl | Unable to setup SSH connection on channel: [id: 0x02ed4d20] java.net.ConnectException: Connection refused at sun.nio.ch.SocketChannelImpl.checkConnect(Native Method)[:1.8.0_65] at sun.nio.ch.SocketChannelImpl.finishConnect(SocketChannelImpl.java:717)[:1.8.0_65] at org.apache.mina.transport.socket.nio.NioSocketConnector.finishConnect(NioSocketConnector.java:221)[257:org.apache.mina.core:2.0.7] at org.apache.mina.transport.socket.nio.NioSocketConnector.finishConnect(NioSocketConnector.java:46)[257:org.apache.mina.core:2.0.7] at org.apache.mina.core.polling.AbstractPollingIoConnector.processConnections(AbstractPollingIoConnector.java:442)[257:org.apache.mina.core:2.0.7] at org.apache.mina.core.polling.AbstractPollingIoConnector.access$700(AbstractPollingIoConnector.java:64)[257:org.apache.mina.core:2.0.7] at org.apache.mina.core.polling.AbstractPollingIoConnector$Connector.run(AbstractPollingIoConnector.java:510)[257:org.apache.mina.core:2.0.7] at org.apache.mina.util.NamePreservingRunnable.run(NamePreservingRunnable.java:64)[257:org.apache.mina.core:2.0.7] at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)[:1.8.0_65] at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)[:1.8.0_65]

`See flowchart <http://i.imgur.com/2fwTem4.png>`__

**Description of the situation:**
Test suites are run to verify ODL controller is able to connect to a Netconf device and perform CRUD operations. In this instance the Netconf device is isolated from the cluster using a firewall on the host VM.

**Test Names:**
This error can occur in any of these suites:

csit/suites/netconf/clustering/CRUD.robot
csit/suites/netconf/clustering/isolate_device_from_1node.robot
csit/suites/netconf/clustering/isolate_device_from_3nodes.robot

**Test Execution:**

.. code-block:: text

       pybot -N netconf-clustering.txt --removekeywords wuks -c critical -e exclude -v BUNDLEFOLDER:distribution-karaf-0.4.5-SNAPSHOT -v WORKSPACE:/tmp -v BUNDLE_URL:https://nexus.opendaylight.org/content/repositories/opendaylight.snapshot/org/opendaylight/integration/distribution-karaf/0.4.5-SNAPSHOT/distribution-karaf-0.4.5-20161219.002820-405.zip -v JAVA_HOME:/usr/lib/jvm/java-1.7.0 -v NEXUSURL_PREFIX:https://nexus.opendaylight.org -v JDKVERSION:openjdk7 -v ODL_STREAM:beryllium -v CONTROLLER:10.29.12.125 -v CONTROLLER1:10.29.12.137 -v CONTROLLER2:10.29.12.73 -v ODL_SYSTEM_IP:10.29.12.125 -v ODL_SYSTEM_1_IP:10.29.12.125 -v ODL_SYSTEM_2_IP:10.29.12.137 -v ODL_SYSTEM_3_IP:10.29.12.73 -v NUM_ODL_SYSTEM:3 -v CONTROLLER_USER:jenkins -v ODL_SYSTEM_USER:jenkins -v TOOLS_SYSTEM_IP:10.29.13.47 -v TOOLS_SYSTEM_1_IP:10.29.13.47 -v NUM_TOOLS_SYSTEM:1 -v TOOLS_SYSTEM_USER:jenkins -v MININET:10.29.13.47 -v MININET1: -v MININET2: -v MININET_USER:jenkins -v USER_HOME:/home/jenkins -v USE_NETCONF_CONNECTOR:False csit/suites/netconf/clustering/CRUD.robot

**Objective of test:**
This test focuses on testing Netconf end to end functionality with a Netconf device. The Netconf device must be contactable on the network layer.

**Correction Steps:**
Failure of these test suites indicates that clustering functionality is impaired. Verify the test setup and test environment are correctly configured then raise a bug report.

Log Excerpt 24:
~~~~~~~~~~~~~~~

**Log Message:**

.. code-block:: text

   2016-12-19 01:33:11,459 | WARN | qtp1913987690-54 | BrokerFacade | 243 - org.opendaylight.netconf.sal-rest-connector - 1.3.4.Beryllium-SR4_6-frinxodl | Exception by reading CONFIGURATION via Restconf: / java.util.concurrent.ExecutionException: java.lang.IllegalStateException: Unknown child(ren) node(s) detected, identified by: (urn:alcatel-lucent.com:sros:ns:yang:conf-r13)configure, in: org.opendaylight.netconf.sal.connect.netconf.util.NodeContainerProxy@1aeb52a6 at org.opendaylight.yangtools.util.concurrent.MappingCheckedFuture.wrapInExecutionException(MappingCheckedFuture.java:63) at org.opendaylight.yangtools.util.concurrent.MappingCheckedFuture.get(MappingCheckedFuture.java:76)

**Description of the situation:**
Test suites are run to verify that an ODL controller is able to connect to, mount an SROS 14 Netconf device. In this instance the Netconf SROS14 device cannot be fully mounted by a cluster member, pulling information from the device is not possible because the schema on each side of the netconf connection do not match.

`See flowchart <http://i.imgur.com/2fwTem4.png">`__

**Test Names:**  


* csit/suites/netconf/ready/netconfready.robot 
* csit/suites/netconf/clustering/isolate_device_from_3nodes.robot

**Test Execution:**

.. code-block:: text

       pybot -N netconf-clustering.txt --removekeywords wuks -c critical -e exclude -v BUNDLEFOLDER:distribution-karaf-0.4.5-SNAPSHOT -v WORKSPACE:/tmp -v BUNDLE_URL:https://nexus.opendaylight.org/content/repositories/opendaylight.snapshot/org/opendaylight/integration/distribution-karaf/0.4.5-SNAPSHOT/distribution-karaf-0.4.5-20161219.002820-405.zip -v JAVA_HOME:/usr/lib/jvm/java-1.7.0 -v NEXUSURL_PREFIX:https://nexus.opendaylight.org -v JDKVERSION:openjdk7 -v ODL_STREAM:beryllium -v CONTROLLER:10.29.12.125 -v CONTROLLER1:10.29.12.137 -v CONTROLLER2:10.29.12.73 -v ODL_SYSTEM_IP:10.29.12.125 -v ODL_SYSTEM_1_IP:10.29.12.125 -v ODL_SYSTEM_2_IP:10.29.12.137 -v ODL_SYSTEM_3_IP:10.29.12.73 -v NUM_ODL_SYSTEM:3 -v CONTROLLER_USER:jenkins -v ODL_SYSTEM_USER:jenkins -v TOOLS_SYSTEM_IP:10.29.13.47 -v TOOLS_SYSTEM_1_IP:10.29.13.47 -v NUM_TOOLS_SYSTEM:1 -v TOOLS_SYSTEM_USER:jenkins -v MININET:10.29.13.47 -v MININET1: -v MININET2: -v MININET_USER:jenkins -v USER_HOME:/home/jenkins -v USE_NETCONF_CONNECTOR:False csit/suites/netconf/single_node/sros140R04.robot

**Objective of test:**
This test focuses on testing Netconf end to end functionality with a Netconf device. A Netconf device must be contactable on the network layer and share common schema with ODL. If that is the case we expect to be able to pull data from the device.

**Correction Steps:**
Failure of these test suites indicates that clustering functionality is impaired. Verify the test setup and test environment are correctly configured then raise a bug report.
