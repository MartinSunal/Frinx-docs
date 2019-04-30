
frinx-odl-distribution-3-1-1
----------------------------

This document describes the latest changes, additions, known issues, and fixes for the Frinx ODL Distribution.

**Note:** Frinx CLI plugin safe command execution - Frinx ODL distribution 3.1.1 introduces safe command execution to the :doc:`Frinx CLI plugin <../FRINX_Features_User_Guide/cli/cli-service-module>`. This forces the cli session to wait for the device to echo back each command before issuing another command. To implement safe command execution, remove the following line (boxed in red in the image below) when issuing the Mount IOS XR cli REST call within Postman. For more info on using the Frinx API and Postman see :doc:`our guide <../API>`.


.. image:: safe-command-execution.png
   :target: safe-command-execution.png
   :alt: safe command execution


**Note that FRINX ODL distribution 3.1.1 requires Java 8 (Openjdk 1.8.0-171 or newer)**
To install Java:
Ubuntu: In a terminal type

.. code-block:: guess

   sudo apt-get install openjdk-8-jre


CentOS: In a terminal type

.. code-block:: guess

   sudo yum install java-1.8.0-openjdk


New Features, Improvements
~~~~~~~~~~~~~~~~~~~~~~~~~~


#. UniConfig framework has been added.
#. Switches to G1 garbage collector.
#. Uses 4GB heap by default.
#. Enables crash on out of memory JVM flag.

Known Issues
~~~~~~~~~~~~


#. odl-netconf-clustered-topology:

   * Contains critical bugs and is not intended for production use, so odl-netconf-topology was modified FRINX so that it can work in cluster. FRINX recommends to use odl-netconf-topology in production environments.

#. restconf/operational/entity-owners:

   * entity-owners contains no data as entity ownership service was rewritten. Entity owners are assigned to the same node that hosts shard leaders.

#. CLI telnet connectivity with reverse telnet on Cisco devices is not supported in this release.
#. CLI, UniConfig framework, and L2/3VPN service modules are supported on single node ODL.
#. Readers returning default data for non-existent instances.

   * When a specific query is issued for some child reader e.g. AreaReader in OSPF for XR, it will return default data back instead of a 404 response.

#. UniConfig:

   * Connection status is not shown under unified node
   * *Workaround is to check connection status under CLI/NETCONF node*
   * Uniconfig node is not removed from CONF DS when CLI/NETCONF node is unmount
   * *Workaround is to remove uniconfig node manually e.g.:*
     
.. code-block:: guess

        curl -X DELETE \
        http://192.168.56.11:8181/restconf/config/network-topology:network-topology/topology/uniconfig/node/IOSXR
        -H 'content-type: application/json'

   * Create/update/delete of BFD attributes on LAG does not work
   * Create/read/update/delete of ACL does not work properly
   * Create/read/update/delete of SNMP does not work properly

Opendaylight Carbon Release Notes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Frinx controller 3.1.1 is based on OpenDaylight Carbon.

https://wiki.opendaylight.org/view/Simultaneous_Release/Carbon/Release_Notes
https://wiki.opendaylight.org/view/Simultaneous_Release:Carbon_Release_Plan
https://wiki.opendaylight.org/view/BGP_LS_PCEP:Carbon_Release_Notes
