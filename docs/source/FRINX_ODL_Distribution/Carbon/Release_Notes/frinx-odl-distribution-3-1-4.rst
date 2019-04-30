
frinx-odl-distribution-3-1-4
----------------------------

This document describes the latest changes, additions, known issues, and fixes for the Frinx ODL Distribution.

**Note that FRINX ODL distribution 3.1.4 requires Java 8 (Openjdk 1.8.0-171 or newer)**
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

  * Speed up cli mount process by changing prompt resolution strategy - Instead of a default wait of 5 seconds for prompt to be received from device, use a different strategy where wait time is gradually increased starting from 1 second to 10 seconds. This speeds up mount time significantly.
  * Speed up command execution when changing prompt - When executing a command and also switching prompt, use the new prompt resolution strategy to speed up the process. This speeds up transactions for devices such as XR.
  * Do not check output of “execute-and-read” RPC against device specific error patterns - Just propagate the errors as rpc output

* Unified layer and unitopo-units

  * Speed up cli mount process by caching AST representation of YANG models - Yang text to AST parsing was performed multiple times when mounting each device. The process took around 50% of time spent while mounting a netconf device. Now with a cache, the parsing happens only once

* UniConfig framework

  * Make rollback on a failed node more robust - When rolling back a node from unified topology which failed during transaction, make sure to check whether rollback is necessary. If it is necessary (native rollback failed), read current state before pushing previous state. 

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

The Frinx controller 3.1.4 is based on OpenDaylight Carbon.

https://wiki.opendaylight.org/view/Simultaneous_Release/Carbon/Release_Notes
https://wiki.opendaylight.org/view/Simultaneous_Release:Carbon_Release_Plan
https://wiki.opendaylight.org/view/BGP_LS_PCEP:Carbon_Release_Notes
