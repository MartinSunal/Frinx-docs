
frinx-odl-distribution-3-1-7
----------------------------

This document describes the latest changes, additions, known issues, and fixes for the Frinx ODL Distribution.

**Note that FRINX ODL distribution 3.1.7 requires Java 8 (Openjdk 1.8.0-171 or newer)**
To install Java:
Ubuntu: In a terminal type

.. code-block:: guess

   sudo apt-get install openjdk-8-jre


CentOS: In a terminal type

.. code-block:: guess

   sudo yum install java-1.8.0-openjdk


New Features, Improvements
^^^^^^^^^^^^^^^^^^^^^^^^^^

CLI UNITOPO units (Device features)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


* **Cisco IOS XR [5.\ *, 6.*\ ] - cli**

  * **HSRP support**
  * **BFD support** - with openconfig native model, as an alternative to frinx extension module (We should use openconfig models instead of custom extensions when available)
  * **LACP support** - with openconfig native model, as an alternative to frinx extension module (We should use openconfig models instead of custom extensions when available)

* **DASAN [*] - cli**

  * **Interface support**
  * **Configuration-metadata support** - last commit timestamp retrieval
  * **VLAN support**

* **Cisco NEXUS [*] - cli**

  * **Interface support**
  * **Configuration-metadata support** - last commit timestamp retrieval

* **Junos [14.*] - cli**

  * **Interface support**
  * **Network-instance (VRF) support**
  * **ACL support**
  * **OSPF support**
  * **Routing policy support**
  * **Configuration-metadata support** - last commit timestamp retrieval

* **Cisco IOS XR [6.*] - NETCONF**

  * **Network instance (VRF) support**
  * **OSPF support**
  * **Configuration-metadata support** - last commit timestamp retrieval

* **Cisco IOS XR [7.*] - NETCONF**

  * **Interface support**
  * **Network instance (VRF) support**
  * **EVPN support**
  * **BGP support**
  * **OSPF support**
  * **Logging support**
  * **Configuration-metadata support** - last commit timestamp retrieval

* **Junos [17.*] - NETCONF**

  * **BFD support** - with openconfig native model, as an alternative to frinx extension module (We should use openconfig models instead of custom extensions when available)
  * **LACP support** - with openconfig native model, as an alternative to frinx extension module (We should use openconfig models instead of custom extensions when available)

* **Junos [18.*] - NETCONF**

  * **Interface support**
  * **Network instance (VRF) support**
  * **BGP support**
  * **ACL support**
  * **Probe support**
  * **Configuration-metadata support** - last commit timestamp retrieval

Yangtools
~~~~~~~~~


* Reduce size of comments in generated classes

Distribution
~~~~~~~~~~~~


* **Light-weight distribution for uniconfig** - Contains only uniconfig, restconf, netconf southbound, cli southbound and unified topology. This makes the distribution smaller and the startup is also faster. It can be used as a replacement to the full distribution as long as it is used for uniconfig related use cases.

CLI topology
~~~~~~~~~~~~


* Remove outdated unit-archetype
* Reduce log output on DEBUG level

Unified topology
~~~~~~~~~~~~~~~~


* **AutoCommit is set to TRUE by default for all netconf sessions** - Auto commit sends any update to netconf devices in a dedicated transaction. This is to work around netconf issues of Network devices. To disable the auto commit feature for a specific device, set useAutoCommit() to false in any of its units

  * **Current settings**:

    * XR 6.1.X      TRUE
    * XR 6.2.X +    FALSE
    * XR 7      FALSE
    * Junos 17      FALSE
    * Junos 18      FALSE

Openconfig
~~~~~~~~~~


* **Added openconfig OSPFv3 protocol models**
* **Added openconfig probes models**
* **Utility to create keyed instance identifier from IIDs constants**
* **Support Augments in IIDs** - Paths under augmentations are now also available in generated IIDs classes

Uniconfig
~~~~~~~~~


* Bugfixing

Translate-unit-docs
~~~~~~~~~~~~~~~~~~~


* **Add support for documenting use-cases within openconfig models** - Allow hand crafted use-case documentation to be integrated with generated openconfig documentation

Swagger
~~~~~~~


* **Uniconfig REST API documented with OpenAPI v2** - OpenAPI document generated from Uniconfig model + Openconfig models
* **Uniconfig client code generated from OpenAPI definition available for Python and Go clients** - Client code library, encapsulating REST calls no available for external applications interacting with Uniconfig
* **Unified REST API documented with OpenAPI v2** - OpenAPI document generated from Unified topology model + Openconfig models
* **Unified client code generated from OpenAPI definition available for Python and Go clients** - Client code library, encapsulating REST calls no available for external applications interacting with unified topology
* **Southbound REST API documented with OpenAPI v2** - OpenAPI document generated from Cli tipology + Netconf topology models
* **Southbound client code generated from OpenAPI definition available for Python and Go clients** - Client code library, encapsulating REST calls no available for external applications interacting with southbound (cli and netconf topology)
* **Example LACP service implementation using generated Swagger based client code**

  * `https://github.com/FRINXio/Lacp-service-labdocs <https://github.com/FRINXio/Lacp-service-labdocs>`__

For more information and download links, please, visit `Swagger documentation page <https://frinxio.github.io/Frinx-docs/FRINX_ODL_Distribution/Carbon/FRINX_Features_User_Guide/swagger-docs.html>`__

L3VPN service module
~~~~~~~~~~~~~~~~~~~~

* **No longer supported** - The L3VPN implementation native to FRINX ODL will no longer be supported

L2VPN service module
~~~~~~~~~~~~~~~~~~~~

* **No longer supported** - The L2VPN implementation native to FRINX ODL will no longer be supported

Known Issues
^^^^^^^^^^^^

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

The Frinx controller 3.1.7 is based on OpenDaylight Carbon.

https://wiki.opendaylight.org/view/Simultaneous_Release/Carbon/Release_Notes
https://wiki.opendaylight.org/view/Simultaneous_Release:Carbon_Release_Plan
https://wiki.opendaylight.org/view/BGP_LS_PCEP:Carbon_Release_Notes
