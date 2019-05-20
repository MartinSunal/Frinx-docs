
UniConfig framework
===================

Mounting CLI or NETCONF network element
---------------------------------------

Mounting CLI or NETCONF network element.


.. image:: drawing/UNM/CLI_NETCONF_mount.png
   :target: drawing/UNM/CLI_NETCONF_mount.html
   :alt: Mount CLI or NETCONF device


CLI
~~~

More information about CLI mounting can be found `here <https://frinxio.github.io/Frinx-docs/FRINX_ODL_Distribution/Carbon/FRINX_Features_User_Guide/cli/cli-service-module.html#mounting-a-cli-device>`__

:download:`cli-topology.yang <cli-topology.yang>`

:download:`Tree representation of a topology <yang/cli-topology.html>`

`Examples in Postman collection <https://github.com/FRINXio/Postman/blob/carbon/development/Uniconfig%20Framework/postman_collection_uniconfig_demo.json>`__

NETCONF
~~~~~~~

More information about NETCONF mounting can be found `here <http://docs.opendaylight.org/en/stable-carbon/user-guide/netconf-user-guide.html#southbound-netconf-connector>`__

:download:`netconf-node-topology.yang <netconf-node-topology.yang>`

:download:`Tree representation of netconf-node-topology.yang <netconf-node-topology.html>`

`Examples in Postman collection <https://github.com/FRINXio/Postman/blob/carbon/development/Uniconfig%20Framework/postman_collection_uniconfig_demo.json>`__

UniConfig API description
-------------------------

:download:`uniconfig-manager.yang <uniconfig-manager.yang>`

*NOTE: mouse hover on elements in tree representation shows description*

:download:`Tree representation of uniconfig-manager.yang <yang/uniconfig-manager.html>`

Config initialization
~~~~~~~~~~~~~~~~~~~~~

Initial reading of network element configuration is triggered when unified
node is created in operational datastore. Reading of data from network element is delegated
to unified mountpoint. Only config data are read from unified mountpoint.
Data is stored as uniconfig node to config and operational datastore.
If uniconfig node already exist in operational datastore, data is stored only
to operational datastore (e.g. in case of reconnect).


.. image:: drawing/UNM/initial_read.png
   :target: drawing/UNM/initial_read.png
   :alt: Initial reading


RPC sync-from-network
~~~~~~~~~~~~~~~~~~~~~

Purpose of this RPC is to synchronize configuration from network
elements to uniconfig nodes in operational datastore
RPC input contains list of uniconfig nodes where to refresh configuration
from network. Empty input means all nodes.


.. image:: drawing/UNM/RPC_sync-from-network.png
   :target: drawing/UNM/RPC_sync-from-network.png
   :alt: RPC sync-from-network


Example
+++++++

RPC input contains list of uniconfig nodes where configuration should be
refreshed.

RPC request:

.. code-block:: guess

   curl -X POST \
     http://192.168.56.11:8181/restconf/operations/uniconfig-manager:sync-from-network \
     -H 'content-type: application/json' \
     -d '{
       "input": {
           "target-nodes": {
               "node": ["IOSXR"]
           }
       }
   }'

RPC response - success:

.. code-block:: guess

   {
       "output": {
           "node-sync-status-results": {
               "overall-sync-status": "complete",
               "node-sync-status-result": [
                   {
                       "nodeId": "IOSXR"
                   }
               ]
           }
       }
   }

RPC commit
~~~~~~~~~~

External or ODL application stores intended configuration under nodes
in uniconfig topology. Trigger for execution of configuration is RPC
commit.
RPC input contains list of uniconfig nodes where to commit configuration.
Empty input means all nodes. Output of RPC describes result of commit.

`List of supported configuration data <https://github.com/FRINXio/translation-units-docs/tree/master/Configuration%20datasets>`__

Successful RPC commit is on figure below.


.. image:: drawing/UNM/RPC_commit_success.png
   :target: drawing/UNM/RPC_commit_success.png
   :alt: RPC commit success case


Failed RPC commit with automatic rollback is on figure below.


.. image:: drawing/UNM/RPC_commit_fail.png
   :target: drawing/UNM/RPC_commit_fail.png
   :alt: RPC commit fail case


Example
+++++++

RPC commit input has one target node and output describes result of commit.

RPC request:

.. code-block:: guess

   curl -X POST \
     http://192.168.56.11:8181/restconf/operations/uniconfig-manager:commit \
     -H 'content-type: application/json' \
     -d '{
       "input": {
           "target-nodes": {
               "node": ["IOSXR"]
           },
       "do-rollback": true
       }
   }'

RPC response - success:

.. code-block:: guess

   {
       "output": {
           "overall-configuration-status": "complete",
           "node-config-results": {
               "node-config-result": [
                   {
                       "nodeId": "IOSXR",
                       "configuration-status": "complete"
                   }
               ]
           }
       }
   }

RPC checked-commit
~~~~~~~~~~~~~~~~~~

External or ODL application stores intended configuration under nodes
in uniconfig topology. Trigger for execution of checked configuration is
RPC checked-commit. Checked commit is similar to RPC commit but it also
checks if nodes are in sync with network before configuration.
RPC fails if some node is out of sync.
RPC input contains list of uniconfig nodes where to commit configuration.
Empty input means all nodes. Output of RPC describes result of commit.

`List of supported configuration data <https://github.com/FRINXio/translation-units-docs/tree/master/Configuration%20datasets>`__

Successful RPC checked-commit is on figure below.


.. image:: drawing/UNM/RPC_checked-commit_success.png
   :target: drawing/UNM/RPC_checked-commit_success.png
   :alt: RPC checked-commit success case


Failed RPC checked-commit before configuration is on figure below.


.. image:: drawing/UNM/RPC_checked-commit_fail.png
   :target: drawing/UNM/RPC_checked-commit_fail.png
   :alt: RPC checked-commit fail case


Example
+++++++

RPC commit input has one target node and output describes result of checked-commit.

RPC request:

.. code-block:: guess

   curl -X POST \
     http://192.168.56.11:8181/restconf/operations/uniconfig-manager:checked-commit 
     -H 'content-type: application/json' \
     -d '{
       "input": {
           "target-nodes": {
               "node": ["IOSXR"]
           },
       "do-rollback": true
       }
   }'

RPC response - success:

.. code-block:: guess

   {
       "output": {
           "overall-configuration-status": "complete",
           "node-config-results": {
               "node-config-result": [
                   {
                       "nodeId": "IOSXR",
                       "configuration-status": "complete"
                   }
               ]
           }
       }
   }

RPC calculate-diff
~~~~~~~~~~~~~~~~~~

This RPC creates diff between actual uniconfig topology and intended
uniconfig topology.
RPC input contains list of uniconfig nodes where to calculate diff.
Empty input means all nodes. Output contains list of statements representing diff.


.. image:: drawing/UNM/RPC_calculate-diff.png
   :target: drawing/UNM/RPC_calculate-diff.png
   :alt: RPC calculate-diff


Example
+++++++

RPC commit input has one target node and output contains list of statements representing
diff.

RPC request:

.. code-block:: guess

   curl -X POST \
     http://192.168.56.11:8181/restconf/operations/uniconfig-manager:calculate-diff \
     -H 'content-type: application/json' \
     -H 'accept: application/xml' \
     -d '{
       "input": {
           "target-nodes": {
               "node": ["IOSXR"]
           },
       }
   }'

RPC response - success:

.. code-block:: guess

   <output xmlns="urn:opendaylight:params:xml:ns:yang:uniconfig:manager">
       <node-with-diff>
           <node-id>IOSXR</node-id>
           <created-data>
               <path xmlns:c="http://frinx.openconfig.net/yang/interfaces" xmlns:b="http://frinx.openconfig.net/yang/uniconfig-topology" xmlns:d="http://frinx.openconfig.net/yang/interfaces/ethernet" xmlns:a="urn:TBD:params:xml:ns:yang:network-topology">/a:network-topology/a:topology[a:topology-id='uniconfig']/a:node[a:node-id='IOSXR']/b:configuration/c:interfaces/c:interface[c:name='GigabitEthernet0/0/0/3']/d:ethernet/d:config</path>
               <data>{
     "frinx-openconfig-if-ethernet:config": {
       "frinx-lacp-lag-member:lacp-mode": "ACTIVE",
       "frinx-lacp-lag-member:interval": "SLOW",
       "frinx-openconfig-if-aggregate:aggregate-id": "Bundle-Ether10",
       "auto-negotiate": true,
       "enable-flow-control": false
     }
   }</data>
           </created-data>
       </node-with-diff>
   </output>

RPC replace-config-with-operational
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

RPC replaces uniconfig topology in config datastore with uniconfig
topology from operational datastore.
RPC input contains list of uniconfig nodes to replace from operational
to config datastore. Empty input means all nodes.
PRC output contains result of operation.


.. image:: drawing/UNM/RPC_replace-config-with-operational.png
   :target: drawing/UNM/RPC_replace-config-with-operational.png
   :alt: RPC replace-config-with-operational


Example
+++++++

RPC commit input has one target node and RPC output contains result of operation.

RPC request:

.. code-block:: guess

   curl -X POST \
     http://192.168.56.11:8181/restconf/operations/uniconfig-manager:replace-config-with-operational \
     -H 'content-type: application/json' \
     -d '{
       "input": {
           "target-nodes": {
               "node": ["IOSXR"]
           }
       }
   }'

RPC response - success:

.. code-block:: guess

   {
       "output": {
           "result": "complete"
       }
   }

Snapshot Manager
================

Snapshot manager creates and deletes uniconfig snapshots of
actual uniconfig topology. Snapshots may be used for manual rollback
when intended uniconfig nodes are overwritten by snapshot nodes.

Snapshot Manager API description
--------------------------------

:download:`snapshot-manager.yang <snapshot-manager.yang>`

*NOTE: mouse hover on elements in tree representation shows description*

:download:`Tree representation of snapshot-manager.yang <snapshot-manager.html>`

Obtaining snapshots-metadata
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Snapshots metadata contain limit (number of snapshots which may be created)
and list of created snapshots with date of creation.

RPC request:

.. code-block:: guess

   curl -X GET \
     http://192.168.56.11:8181/restconf/config/snapshot-manager:snapshots-metadata \
     -H 'Accept: application/json'

RPC response - success:

.. code-block:: guess

   {
       "snapshots-metadata": {
           "limit": 3,
           "snapshot": [
               {
                   "name": "first",
                   "creation-time": "2018-04-25T10:37:57+02:00"
               }
           ]
       }
   }

RPC maximum-snapshot-limit
~~~~~~~~~~~~~~~~~~~~~~~~~~

RPC maximum-snapshot-limit sets limit how many snapshots can be created.
RPC input contains limit of snapshot. RPC output contains result of operation.

Example
+++++++

RPC input contains number of snapshots which may be created. RPC output contains
result of operation.

RPC request:

.. code-block:: guess

   curl -X POST \
     http://192.168.56.11:8181/restconf/operations/snapshot-manager:maximum-snapshot-limit \
     -H 'Content-Type: application/json' \
     -d '{
       "input": {
           "limit": 15
       }
   }'

RPC response - success:

.. code-block:: guess

   {
       "output": {
           "result": "complete"
       }
   }

RPC create-snapshot
~~~~~~~~~~~~~~~~~~~

RPC creates snapshot of uniconfig topology. This snapshot can be used
for manual rollback.
RPC input contains name for topology snapshot. RPC output contains
result of operation.


.. image:: drawing/UNM/RPC_create-snapshot.png
   :target: drawing/UNM/RPC_create-snapshot.png
   :alt: RPC create-snapshot


Example
+++++++

RPC input contains name for topology snapshot. RPC output contains
result of operation.

RPC request:

.. code-block:: guess

   curl -X POST \
     http://192.168.56.11:8181/restconf/operations/snapshot-manager:create-snapshot \
     -H 'content-type: application/json' \
     -d '{
       "input": {
           "name": "snapshot1"
       }
   }'

RPC response - success:

.. code-block:: guess

   {
       "output": {
           "result": "complete"
       }
   }

RPC delete-snapshot
~~~~~~~~~~~~~~~~~~~

RPC removes snapshot from CONF DS.
RPC input contains name of topology snapshot which should be removed.
RPC output contains result of operation.


.. image:: drawing/UNM/RPC_delete-snapshot.png
   :target: drawing/UNM/RPC_delete-snapshot.png
   :alt: RPC delete-snapshot


Example
+++++++

RPC input contains name of topology snapshot which should be removed.
RPC output contains result of operation.

RPC request:

.. code-block:: guess

   curl -X POST \
     http://192.168.56.11:8181/restconf/operations/snapshot-manager:delete-snapshot \
     -H 'content-type: application/json' \
     -d '{
       "input": {
           "name": "snapshot1"
       }
   }'

RPC response - success:

.. code-block:: guess

   {
       "output": {
           "result": "complete"
       }
   }

RPC replace-config-with-snapshot
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

RPC replaces uniconfig topology in config datastore with snapshot containing only
target nodes.
RPC input contains name of topology snapshot and target nodes which should replace
uniconfig nodes in config datastore. PRC output contains result of operation.


.. image:: drawing/UNM/RPC_replace-config-with-snapshot.png
   :target: drawing/UNM/RPC_replace-config-with-snapshot.png
   :alt: RPC replace-config-with-snapshot


Example
+++++++

RPC input contains name of topology snapshot which should replace uniconfig
topology in config datastore. RPC output contains result of operation.

RPC request:

.. code-block:: guess

   curl -X POST \
     http://192.168.56.11:8181/restconf/operations/snapshot-manager:replace-config-with-snapshot \
     -H 'content-type: application/json' \
     -d '{
       "input": {
           "name": "snapshot1",
           "target-nodes": {
               "node": ["IOSXR"]
           }
       }
   }'

RPC response - success:

.. code-block:: guess

   {
       "output": {
           "result": "complete"
       }
   }

Dry-run Manager
===============

The manager provides functionality showing CLI commands which would be
sent to network element.
This manager uses Uniconfig Node Manager for diff calculation between
actual and intended state and cli-dryrun mountpoint for application of
changes.

Dry-run Manager API description
-------------------------------

RPC dry-run
~~~~~~~~~~~

The RPC resolves diff between actual and intended configuration by using
Uniconfig Node Manager. After diff is resolved it filters only changes
made on cli nodes. Changes for cli nodes are applied by using cli-dryrun
mountpoint which only stores translated CLI commands to cli-dry-run journal.
After all changes are applied, cli-dryrun journal is read and RPC output
is created and returned.
RPC input contains list of uniconfig nodes for which to execute dry run.
Empty input means all nodes. Output contains list of commands for the given nodes.


.. image:: drawing/dry-run_manager/RPC_dry-run.png
   :target: drawing/dry-run_manager/RPC_dry-run.png
   :alt: RPC dry-run


Example
+++++++

RPC input contains target node and output contains list of commands which
would be send to device if RPC commit/checked-commit was called.

RPC request:

.. code-block:: guess

   curl -X POST \
     http://192.168.56.11:8181/restconf/operations/dryrun-manager:dryrun-commit \
     -H 'Accept: application/xml' \
     -H 'Content-Type: application/json' \
     -d '{
       "input": {
           "target-nodes": {
               "node": ["IOSXR"]
           }
       }
   }'

RPC response - success:

.. code-block:: guess

   <output xmlns="urn:opendaylight:params:xml:ns:yang:dryrun:manager">
       <overall-configuration-status>complete</overall-configuration-status>
       <node-config-results>
           <node-config-result>
               <nodeId>iosxr</nodeId>
               <configuration-status>complete</configuration-status>
               <cli-configuration>2018-05-22T15:49:50.095: configure terminal
   2018-05-22T15:49:50.099: interface GigabitEthernet0/0/0/1
   no ipv4 address 20.20.20.21 255.255.255.0
   root

   2018-05-22T15:49:50.104: interface GigabitEthernet0/0/0/1
   no mtu
   description some description iosxr
   no shutdown
   root

   2018-05-22T15:49:50.105: interface GigabitEthernet0/0/0/1
   ipv4 address 20.20.20.20 255.255.255.0
   root

   2018-05-22T15:49:50.108: commit
   2018-05-22T15:49:50.109: end
   </cli-configuration>
           </node-config-result>
       </node-config-results>
   </output>
