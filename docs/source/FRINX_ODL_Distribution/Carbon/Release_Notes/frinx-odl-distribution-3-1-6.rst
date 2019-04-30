
frinx-odl-distribution-3-1-6
----------------------------

This document describes the latest changes, additions, known issues, and fixes for the Frinx ODL Distribution.

**Note that FRINX ODL distribution 3.1.6 requires Java 8 (Openjdk 1.8.0-171 or newer)**
To install Java:\ :raw-html-m2r:`<br>`
Ubuntu: In a terminal type

.. code-block:: guess

   sudo apt-get install openjdk-8-jre


CentOS: In a terminal type

.. code-block:: guess

   sudo yum install java-1.8.0-openjdk


New Features, Improvements
~~~~~~~~~~~~~~~~~~~~~~~~~~

* CLI and UNITOPO units (Device features)

  * LLDP unit added for IOS XR over CLI - Support for LLDP information retrieval from Cisco Nexus device using CLI
  * MPLS LDP management support added for Cisco IOS XR over CLI
  * VRF awareness added to BGP and OSPF management for Cisco IOS XR over CLI
  * Basic device info for Cisco IOS XR over CLI expanded - Now also returns OS type and version
    within openconfig-platform/components data
  * Basic device info for Cisco IOS over CLI expanded - Now also returns OS type and version
  * Basic device info for Huawei VRP over CLI added - Provides OS type and version as well as list of installed hardware components

* Translate-unit-docs

  * Initial version of automatic documentation for cli and unitopo units added - Generated documentation can be found at https://frinxio.github.io/translation-units-docs/
  * This documentation shows the entire Openconfig data tree with added information about Readers and Writers implemented in projects cli-units and unitopo-units

* CLI topology

  * Reconnect counter added to CLI topology - Operational data in CLI topology now show number of connect attempts when unable to mount a device
  * Commit error patterns decoupled from error patterns - Error patterns checked against the output of “commit” command (or equivalent) is now in a dedicated set. No longer part of regular error patterns
  * Allow error pattern management via Datastore - Error patterns and commit error patterns can be viewed and modified even at runtime using northbound interfaces
  * Extended journaling capabilities - CLI journal can now record detailed information in addition to recording actual commands. Specifically: transaction lifecycle information and detailed information about handler invocations. This feature is by default turned off and can be used by setting parameter “journal-level” to “extended” when mounting a CLI device
  * Journal disappearance after hitting its limit was fixed, now only half of recorded data is removed once limit is hit

* Uniconfig

  * Make rollback from uniconfig optional - When an error is detected while configuring the network, uniconfig will by default rollback devices which have been successfully configured. This behavior can now be turned of using new “do-rollback” parameter of commit and checked-commit rpcs. If rollback is turned off, uniconfig will preserve partial changes to the network (except the device which failed).

* Group Based Policy

  * Include contract-ref to policy resolution process - Contract selection phase uses named-selectors from EPGs, contracts and contract-refs from local tenant where EPG is located. If named-selector matches against contract-ref then a contract is read from referenced tenant.
  * Added odl-groupbasedpolicy-noop feature - this feature does not register data change listener for tenant, so policy resolution process is not executed automatically

* Karaf

  * Make Karaf console compatible with Ubuntu 18.04 - fixed issues with command history, autocomplete, etc.

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

The Frinx controller 3.1.6 is based on OpenDaylight Carbon.

https://wiki.opendaylight.org/view/Simultaneous_Release/Carbon/Release_Notes
https://wiki.opendaylight.org/view/Simultaneous_Release:Carbon_Release_Plan
https://wiki.opendaylight.org/view/BGP_LS_PCEP:Carbon_Release_Notes
