
NETCONF Overview and use within the FRINX ODL Distribution
===========================================================

Overview
--------

NETCONF is an Internet Engineering Task Force (IETF) protocol used for configuration and monitoring devices in the network. It can be used to “create, recover, update, and delete configurations of network devices”. The base NETCONF protocol is described in `RFC-6241 <https://tools.ietf.org/html/rfc6241>`__.  

NETCONF operations are overlaid on the Remote Procedure Call (RPC) layer and may be described in either XML or JSON.  

**NETCONF in OpenDaylight:**  

OpenDaylight supports the NETCONF protocol as a northbound server as well as a southbound plugin. It also includes a set of test tools for simulating NETCONF devices and clients.  

Southbound (netconf-connector)
------------------------------

The NETCONF southbound plugin is capable of connecting to remote NETCONF devices and exposing their configuration/operational datastores, RPCs and notifications as MD-SAL mount points. These mount points allow applications and remote users (over RESTCONF) to interact with the mounted devices.  

In terms of RFCs, the connector supports:  

`RFC-6241 <https://tools.ietf.org/html/rfc6241>`__
`RFC-5277 <https://tools.ietf.org/html/rfc5277>`__
`RFC-6022 <https://tools.ietf.org/html/rfc6022>`__
`draft-ietf-netconf-yang-library-06 <https://tools.ietf.org/html/draft-ietf-netconf-yang-library-06>`__  

**Netconf-connector is fully model-driven (utilizing the YANG modeling language) so in addition to the above RFCs, it supports any data/RPC/notifications described by a YANG model that is implemented by the device.**  

*Tip
NETCONF southbound can be activated by installing odl-netconf-connector-all Karaf feature.*  

Netconf-connector configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are 2 ways for configuring netconf-connector: NETCONF or RESTCONF. This guide focuses on using RESTCONF.  

Default configuration
+++++++++++++++++++++

The default configuration contains all the necessary dependencies (file: 01-netconf.xml) and a single instance of netconf-connector (file: 99-netconf-connector.xml) called **controller-config** which connects itself to the NETCONF northbound in OpenDaylight in a loopback fashion. The connector mounts the NETCONF server for config-subsystem in order to enable RESTCONF protocol for config-subsystem. This RESTCONF still goes via NETCONF, but using RESTCONF is much more user friendly than using NETCONF.  

Spawning additional netconf-connectors while the controller is running
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Preconditions:  


1. OpenDaylight is running
2. In Karaf, you must have the netconf-connector installed (at the Karaf prompt, type: feature:install odl-netconf-connector-all); the loopback NETCONF mountpoint will be automatically configured and activated
3. Wait until log displays following entry: RemoteDevice{controller-config}: NETCONF connector initialized successfully
   To configure a new netconf-connector you need to send following request to RESTCONF:

.. code-block:: text

      POST   http://localhost:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/controller-config/yang-ext:mount/config:modules
   Headers:  


* Accept application/xml
* Content-Type application/xml

.. code-block:: text

   <module xmlns="urn:opendaylight:params:xml:ns:yang:controller:config">
     <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">prefix:sal-netconf-connector</type>
     <name>new-netconf-device</name>
     <address xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">127.0.0.1</address>
     <port xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">830</port>
     <username xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">admin</username>
     <password xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">admin</password>
     <tcp-only xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">false</tcp-only>
     <event-executor xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
       <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:netty">prefix:netty-event-executor</type>
       <name>global-event-executor</name>
     </event-executor>
     <binding-registry xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
       <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:md:sal:binding">prefix:binding-broker-osgi-registry</type>
       <name>binding-osgi-broker</name>
     </binding-registry>
     <dom-registry xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
       <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:md:sal:dom">prefix:dom-broker-osgi-registry</type>
       <name>dom-broker</name>
     </dom-registry>
     <client-dispatcher xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
       <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:config:netconf">prefix:netconf-client-dispatcher</type>
       <name>global-netconf-dispatcher</name>
     </client-dispatcher>
     <processing-executor xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
       <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:threadpool">prefix:threadpool</type>
       <name>global-netconf-processing-executor</name>
     </processing-executor>
     <keepalive-executor xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
       <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:threadpool">prefix:scheduled-threadpool</type>
       <name>global-netconf-ssh-scheduled-executor</name>
     </keepalive-executor>
   </module>

This spawns a new netconf-connector which tries to connect to (or mount) a NETCONF device at 127.0.0.1 and port 830. You can check the configuration of config-subsystem’s configuration datastore. The new netconf-connector will now be present there. Just invoke:  

.. code-block:: text

   GET   http://localhost:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/controller-config/yang-ext:mount/config:modules

The response will contain the module for new-netconf-device.  

Right after the new netconf-connector is created, it writes some useful metadata into the datastore of MD-SAL under the network-topology subtree. This metadata can be found at:  

.. code-block:: text

   GET http://localhost:8181/restconf/operational/network-topology:network-topology/

Information about connection status, device capabilities, etc. can be found there.  

Connecting to a device not supporting NETCONF monitoring
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The netconf-connector in OpenDaylight relies on ietf-netconf-monitoring support when connecting to remote NETCONF device. The ietf-netconf-monitoring support allows netconf-connector to list and download all YANG schemas that are used by the device. NETCONF connector can only communicate with a device if it knows the set of used schemas (or at least a subset). However, some devices use YANG models internally but do not support NETCONF monitoring. Netconf-connector can also communicate with these devices, but you have to side load the necessary yang models into OpenDaylight’s YANG model cache for netconf-connector. In general there are 2 situations you might encounter:  


1. **NETCONF device does not support ietf-netconf-monitoring but it does list all its YANG models as capabilities in HELLO message**  

This could be a device that internally uses only ietf-inet-types YANG model with revision 2010-09-24. In the HELLO message that is sent from this device there is this capability reported:

.. code-block:: text

   urn:ietf:params:xml:ns:yang:ietf-inet-types?module=ietf-inet-types&revision=2010-09-24
   For such devices you only need to put the schema into folder cache/schema inside your Karaf distribution.

**Important**
*The file with YANG schema for ietf-inet-types has to be called ietf-inet-types@2010-09-24.yang. It is the required naming format of the cache.*  


2. **NETCONF device does not support ietf-netconf-monitoring and it does NOT list its YANG models as capabilities in HELLO message**

Compared to device that lists its YANG models in HELLO message, in this case there would be no capability with ietf-inet-types in the HELLO message. This type of device basically provides no information about the YANG schemas it uses so its up to the user of OpenDaylight to properly configure netconf-connector for this device.  

Netconf-connector has an optional configuration attribute called yang-module-capabilities and this attribute can contain a list of “YANG module based” capabilities. So by setting this configuration attribute, it is possible to override the “yang-module-based” capabilities reported in HELLO message of the device. To do this, we need to modify the configuration of netconf-connector by adding this XML (It needs to be added next to the address, port, username etc. configuration elements):  

.. code-block:: text

   <yang-module-capabilities xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
     <capability xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
       urn:ietf:params:xml:ns:yang:ietf-inet-types?module=ietf-inet-types&amp;revision=2010-09-24
     </capability>
   </yang-module-capabilities>

**Remember to also put the YANG schemas into the cache folder.**  

**Note**
*For putting multiple capabilities, you just need to replicate the capability xml element inside yang-module-capability element. Capability element is modeled as a leaf-list. With this configuration, we would make the remote device report usage of ietf-inet-types in the eyes of netconf-connector.*  

Reconfiguring Netconf-Connector While the Controller is Running
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

It is possible to change the configuration of a running module while the whole controller is running. This example will continue where the last left off and will change the configuration for the brand new netconf-connector after it was spawned. Using one RESTCONF request, we will change both username and password for the netconf-connector.  

To update an existing netconf-connector you need to send following request to RESTCONF:  

.. code-block:: text

   PUT   http://localhost:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/controller-config/yang-ext:mount/config:modules/module/odl-sal-netconf-connector-cfg:sal-netconf-connector/new-netconf-device

.. code-block:: text

   <module xmlns="urn:opendaylight:params:xml:ns:yang:controller:config">
     <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">prefix:sal-netconf-connector</type>
     <name>new-netconf-device</name>
     <username xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">bob</username>
     <password xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">passwd</password>
     <tcp-only xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">false</tcp-only>
     <event-executor xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
       <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:netty">prefix:netty-event-executor</type>
       <name>global-event-executor</name>
     </event-executor>
     <binding-registry xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
       <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:md:sal:binding">prefix:binding-broker-osgi-registry</type>
       <name>binding-osgi-broker</name>
     </binding-registry>
     <dom-registry xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
       <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:md:sal:dom">prefix:dom-broker-osgi-registry</type>
       <name>dom-broker</name>
     </dom-registry>
     <client-dispatcher xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
       <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:config:netconf">prefix:netconf-client-dispatcher</type>
       <name>global-netconf-dispatcher</name>
     </client-dispatcher>
     <processing-executor xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
       <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:threadpool">prefix:threadpool</type>
       <name>global-netconf-processing-executor</name>
     </processing-executor>
     <keepalive-executor xmlns="urn:opendaylight:params:xml:ns:yang:controller:md:sal:connector:netconf">
       <type xmlns:prefix="urn:opendaylight:params:xml:ns:yang:controller:threadpool">prefix:scheduled-threadpool</type>
       <name>global-netconf-ssh-scheduled-executor</name>
     </keepalive-executor>
   </module>

Since a PUT is a replace operation, the whole configuration must be specified along with the new values for username and password. This should result in a 2xx response and the instance of netconf-connector called new-netconf-device will be reconfigured to use username bob and password passwd. New configuration can be verified by executing:  

.. code-block:: text

   GET   http://localhost:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/controller-config/yang-ext:mount/config:modules/module/odl-sal-netconf-connector-cfg:sal-netconf-connector/new-netconf-device

With new configuration, the old connection will be closed and a new one established.  

Destroying Netconf-Connector While the Controller is Running
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Using RESTCONF one can also destroy an instance of a module. In case of netconf-connector, the module will be destroyed, NETCONF connection dropped and all resources will be cleaned. To do this, simply issue a request to following URL:  

.. code-block:: text

   DELETE   http://localhost:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/controller-config/yang-ext:mount/config:modules/module/odl-sal-netconf-connector-cfg:sal-netconf-connector/new-netconf-device

The last element of the URL is the name of the instance and its predecessor is the type of that module (In our case the type is **sal-netconf-connector** and name **new-netconf-device**). The type and name are actually the keys of the module list.  

Adjusting reconnection settings
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are three configurable parameters from REST API while mounting the device. Through these we can adjust reconnection settings:


1. **Maximum number of connection attempts** - Maximum number of connections retries; when it is reached, the restconf won't try to reconnect to device anymore. By default, this threshold is disabled by value 0.  
2. **Initial timeout between attempts** - The first timeout between reconnection attempts in milliseconds. The default timeout value is set to 2000ms.  
3. **Sleep factor** - After each reconnection attempt, the delay between reconnection attempts is multiplied by this factor. By default, it is set to 1.5 This means that the next delay bewtween attempts will be 3000ms, then it will be 4500ms, etc.

Example of setting of described parameters at creation of netconf mountpoint - maximum connection attempts, initial delay between attempts and sleep factor:

.. code-block:: text

   {
      "node": [
          {
              ...
              "netconf-node-topology:max-connection-attempts": 10,
              "netconf-node-topology:between-attempts-timeout-millis": 8000,
              "netconf-node-topology:sleep-factor": 1.0
          }
      ]
   }

NETCONF test tool
-----------------

Netconf testtool (or netconf device simulator) is a tool that:


* Simulates 1 or more netconf devices
* Is suitable for scale testing
* Uses core implementation of netconf server from ODL controller
* Generates configuration files for controller so that controller distribution (karaf) can easily connect to all simulated devices
* Provides broad configuration options
* Supports notifications

Increase the maximum number of opened files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

How to increase the maximum possible number of opened files - descriptors in Linux system
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

If the buffering file for connection cannot be created on time it can cause continuous reconnection attempts and failure at the end. 
Usually, the default soft limit is set to 1024 and hard limit to 4096. 

Please, open "/etc/security/limits.conf" and modify the following lines (if they already are not defined):

.. code-block:: text

   [user-name] soft nofile 4096
   [user-name] hard nofile 10240


Replace [user-name] by login-name of the user under whom you start ODL and logout-login. 

You can check the current limits using following commands:

.. code-block:: text

   ulimit -Hn
   ulimit -Sn

Soft limit 4096 and hard limit 10240 should be sufficient, but it depends on occupation by other applications and system too).

**NOTE:** Configured value should not reach the one that applies for all users - "cat /proc/sys/fs/file-max".

How does the FRINX ODL Distribution use NETCONF?
------------------------------------------------

The FRINX ODL Distribution's southbound APIs use a NETCONF connector to communicate with downstream devices. The northbound APIs expose the YANG models of connected devices. This makes it possible to examine the operational and config datastores and to configure devices using RPCs. RESTCONF maps a subset of these YANG models to a RESTful interface.

**NETCONF – features used by the FRINX ODL Distribution include:**  

.. list-table::
   :header-rows: 1

   * - Feature name
     - Port
     - Description
     - Protocol
   * - odl-netconf-mdsal
     - 1830
     - Northbound API used by MD-SAL and applications
     - ssh
   * - odl-netconf-connector
     - 8383
     - NETCONF Southbound plugin - configured through the configuration subsystem
     - tcp
   * - odl-restconf
     - 8181
     - RESTCONF Northbound for MD-SAL and applications
     - http

