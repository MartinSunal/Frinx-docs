
frinx-odl-distribution-3-1-3
----------------------------

This document describes the latest changes, additions, known issues, and fixes for the Frinx ODL Distribution.

**Note that FRINX ODL distribution 3.1.3 requires Java 8 (Openjdk 1.8.0-171 or newer)**
To install Java:
Ubuntu: In a terminal type

.. code-block:: guess

   sudo apt-get install openjdk-8-jre


CentOS: In a terminal type

.. code-block:: guess

   sudo yum install java-1.8.0-openjdk


New Features, Improvements
~~~~~~~~~~~~~~~~~~~~~~~~~~


* CLI plugin

  * Fix IO issues causing multiple network connections to device after reconnect
  * Do not return default data for non existing nodes - When reading a non-existing interface via RESTCONF, ODL returned http code 200 with default data in payload, now it returns code 404, as expected
  * Removed “safe-command-execution” from mount request - mount request cannot contain parameter “safe-command-execution” anymore. Behaviour enabled by safe-command-execution is default behaviour now.

* Unified layer and unitopo-units

  * Translate layer caching added to improve mount speed for Unified mountpoints - Subsequent mounts of similar devices are now slightly faster and consume less memory as translate contexts are reused.
  * Do not return default data for non existing nodes - When reading a non-existing interface via RESTCONF, ODL returned http code 200 with default data in payload, now it returns code 404, as expected
  * Add commit-per-update mode for devices managed over netconf - Typically a single transaction in Unified mountpoint is mapped to a single netconf transaction on a device, but due to violations of NETCONF RFC some devices have to be treated in a special way, where 1 transaction in Unified is mapped to N transactions in NETCONF and each change is submitted with a dedicated commit.

* UniConfig framework

  * Configuration metadata (last commit fingerprint) in actual uniconfig node (OPER DS) - Operational data containing configuration fingerprint (timestamp of last configuration obtained from device). This feature can be used to check whether configuration has been updated on a device.
  * Optimized sync-from-network operation thanks to configuration metadata caching - If a device and its units expose last commit fingerprint information, uniconfig will store that information and use it to verify that configuration has been updated on a device, before issuing a full sync-from-network operation. This process speeds up sync-from-network significantly since it only has to issue a full sync-from-network on devices which configuration has really been updated.
  * Snapshot manager - New component in UniConfig to manage snapshots and enforce a limit on their number
  * Operation checked-commit - similar to commit operation but with addition that configuration metadata between device and UniConfig state are compared first. If they are different the checked-commit fails and UniConfig does not configure network elements.
  * Added “target-nodes” to input of RPCs - UniConfig RPCs take “target-nodes” containing list of nodes for which an operation should be executed.

Known Issues
~~~~~~~~~~~~

#. odl-netconf-clustered-topology:

   * Contains critical bugs and is not intended for production use, so odl-netconf-topology was modified by FRINX so that it can work in cluster. FRINX recommends using odl-netconf-topology in production environments.

#. restconf/operational/entity-owners:

   * entity-owners contains no data as entity ownership service was rewritten. Entity owners are assigned to the same node that hosts shard leaders.

#. CLI telnet connectivity with reverse telnet on Cisco devices is not supported in this release.
#. L2/3VPN service modules are supported on single node ODL.
#. Readers returning default data for non-existent instances.

   * When a specific query is issued for a child readers e.g. AreaReader in OSPF for XR, it will return default data back instead of a 404 response.

#. Update in CLI translation units does not work properly - it invokes delete and create operations by default

Opendaylight Carbon Release Notes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Frinx controller 3.1.3 is based on OpenDaylight Carbon.

https://wiki.opendaylight.org/view/Simultaneous_Release/Carbon/Release_Notes
https://wiki.opendaylight.org/view/Simultaneous_Release:Carbon_Release_Plan
https://wiki.opendaylight.org/view/BGP_LS_PCEP:Carbon_Release_Notes
