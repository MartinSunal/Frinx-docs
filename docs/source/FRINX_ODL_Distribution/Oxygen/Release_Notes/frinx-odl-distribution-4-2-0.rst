
FRINX ODL Oxygen 4.2.0. Release Notes
=====================================

This document describes the latest changes, additions, known issues, and fixes for the Frinx ODL Distribution.\ :raw-html-m2r:`<!--more-->`

**Note that FRINX ODL distribution 4.2.0 requires Java 8 (Openjdk 1.8.0-171 or newer)**
To install Java:\ :raw-html-m2r:`<br>`
Ubuntu: In a terminal type

.. code-block:: guess

   sudo apt-get install openjdk-8-jre


CentOS: In a terminal type

.. code-block:: guess

   sudo yum install java-1.8.0-openjdk


New Features, Improvements
--------------------------

Clustering
~~~~~~~~~~

* Upstream cluster - Using upstream clustering implementation with configuration developed by FRINX , which brings more stability to HA deployments.

  * Cluster can now handle more data and more load without crashing
  * Cluster can now distribute (config and operational) shards across cluster and still work reliably

Yangtools
~~~~~~~~~

* **Bugfixing** - mainly to improve compatibility with vendor models

OpenConfig
~~~~~~~~~~

* **Change type for sensitive data from string to encrypted in OpenConfig YANG models** - Encrypted data type is a union of plaintext string and encrypted string, which allows users to submit both plaintext passwords and already encrypted passwords when configuring devices (e.g. bgp neighbor password attribute). This also enables the framework to accept plaintext password from user, but present encrypted version of the password once the configuration is committed, which eliminates plaintext password storage in UniConfig.

CLI
~~~


* **Telnet session authentication hardening** - Telnet session (during authentication) also checks whether the authentication was successful or not and waits for the device’s prompts

* **Lazy CLI connection mode** -  CLI connections can now be configured to only create the underlying SSH/Telnet session when needed and close the session when not used. Compared to default behavior, when the session is kept up as long as possible and reconnected immediately if drops, the lazy mode can save resources especially when managing networks with a large number of devices (thousands).


  * Docs: https://docs.frinx.io/FRINX_ODL_Distribution/Oxygen/FRINX_Features_User_Guide/cli/cli-service-module.html?highlight=lazy#lazycli-mechanism

NETCONF
~~~~~~~


* **Optimize number of edit-config message sent to device by aggregating multiple (non-overlapping) updates into a single edit-config RPC** - So far, each update has been sent out as a dedicated RPC, from now on, netconf southbound will try to collect the updates and send out in a single edit-config RPC. The trigger for sending edit-config is now either commit() invocation or a conflicting operation being executed (path overlap or operation type conflict). This should reduce the communication between ODL and NETCONF capable device.

* **Netconf deadlock over SSH bugfixing** - Fixed a number of threading issues in netconf southbound between mina SSH and Netty which occurred when mounting many netconf devices over unstable network.

* **Dry run** - Netconf supports dry-run operations, so that no configuration is sent to the device. Instead the XML snippets are only recorded in a journal.

UniConfig
~~~~~~~~~

* **UniConfig native** - Enables UniConfig framework to work with native device models to manage configurations. So far, UniConfig was able to manage only unified (OpenConfig) configuration. But with UniConfig native, users can mount NETCONF devices, sync configuration in their native format and continue managing it without the need to develop translation units.

  * Device models are loaded when a device is mounted (connected) into ODL’s global SchemaContext. The loading process is performed completely in runtime, there is no need to manually “pre-compile” the models.
  * Users have to explicitly permit device types by whitelisting their capabilities
  * Example POSTMAN collection: (https://www.getpostman.com/collections/22237f7432181a885563)https://www.getpostman.com/collections/22237f7432181a885563 

* **Ability to blacklist root elements when syncing configuration from devices** - Users can configure blacklisted root configuration items so that they are ignored when reading configuration from devices
  Example POSTMAN collection: https://www.getpostman.com/collections/22237f7432181a885563 

* **Ability to “auto-sync” subtrees after each commit from UniConfig** - Users can configure paths, which will be automatically synced from device after a commit is invoked. This is useful in cases where device changes submitted configuration after it is committed (e.g. password auto encryption). UniConfig will be able to auto-sync with device and store the device update in its databases.

* **Dry-run now available also for NETCONF devices** - dry-run-commit operation now also produces NETCONF payloads

* **Performance improvements and threading model refactoring** - Makes all the components in UniConfig stack capable of parallel processing which increases the performance and scale of the framework

  * Number of threads allocated for each component is automatically determined based on the environment (CPUs available)

Projects no longer supported on Oxygen
--------------------------------------

* frinx-dlux
* frinx-topoprocessing
* frinx-openflowjava
* frinx-openflowplugin
* frinx-neutron
* frinx-sfc
* frinx-ovsdb
* frinx-bgpcep
* frinx-lispflowmapping
* frinx-netvirt
* frinx-honeycomb-vbd
* frinx-infrautils
* frinx-genius
* frinx-federation
* Frinx-dluxapps
* l2vpn
* l3vpn
* Hello-world-samples

Known Issues
------------

BGP
~~~

* When a specific query is issued for a child readers e.g BGP for Junos, it will return default data back instead of a 404 response
* Prefix-limit data for XR5 not implemented
* Update description for multi neighbor for XE not implemented

Daexim
~~~~~~

* Data are lost during export

Opendaylight Oxygen Release Notes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Frinx controller 4.2.0 is based on OpenDaylight Oxygen.

https://wiki.opendaylight.org/view/Simultaneous_Release/Oxygen/Release_Notes
https://wiki.opendaylight.org/view/Simultaneous_Release:Oxygen_Release_Plan
https://wiki.opendaylight.org/view/BGP_LS_PCEP:Oxygen_Release_Notes
