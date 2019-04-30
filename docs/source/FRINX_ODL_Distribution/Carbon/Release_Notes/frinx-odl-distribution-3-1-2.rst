
frinx-odl-distribution-3-1-2
----------------------------

This document describes the latest changes, additions, known issues, and fixes for the Frinx ODL Distribution.
**Note that FRINX ODL distribution 3.1.2 requires Java 8 (Openjdk 1.8.0-171 or newer)**
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

  * Added clustering - tested with 3 node cluster
  * Added option for disabling reconciliation
  * Added caching of command outputs when root element is read

* Unified layer and unitopo-units

  * Implemented translation units for Junos 17.3 using NETCONF
  * Added connection status to unified node in OPER DS
  * Added clustering - tested with 3 node cluster

* UniConfig framework

  * Fixed removing of UniConfig node when CLI/NETCONF node is removed from CONF DS
  * Added clustering - tested with 3 node cluster
  * Dry-run node manager – A “dry run” only counterpart to UniConfig node manager that allows users and apps to verify configuration updates across network before pushing the actual configuration. Dry run produces a list of CLI commands to be executed per device for review. All additional validation implemented by the translation units is also triggered. Works only for CLI devices.

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

The Frinx controller 3.1.2 is based on OpenDaylight Carbon.

https://wiki.opendaylight.org/view/Simultaneous_Release/Carbon/Release_Notes
https://wiki.opendaylight.org/view/Simultaneous_Release:Carbon_Release_Plan
https://wiki.opendaylight.org/view/BGP_LS_PCEP:Carbon_Release_Notes
