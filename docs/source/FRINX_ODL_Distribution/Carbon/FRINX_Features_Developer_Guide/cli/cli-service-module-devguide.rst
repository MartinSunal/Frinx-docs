
CLI
===

* `Building on honeycomb <#building-on-honeycomb>`__
* `Major components <#major-components>`__
* `Modules <#modules>`__
* `Developing a device specific translation unit <#developing-a-device-specific-translation-unit>`__

  * `Installing to Opendaylight <#installing-to-opendaylight>`__

    * `As a feature <#as-a-feature>`__

  * `Testing <#testing>`__
  * `Choosing the right YANG models <#choosing-the-right-yang-models>`__
  * `Implementing handlers <#implementing-handlers>`__

    * `Dependencies between writing handlers (writers) <#dependencies-between-writing-handlers-writers>`__

  * `Implementing RPCs <#implementing-rpcs>`__
  * `Mounting and managing IOS devices from an application <#mounting-and-managing-ios-devices-from-an-application>`__

* `Reading of CLI and device configuraiton <#reading-of-cli-and-device-configuraiton>`__

  * `Process of reading CLI configuration from device <#process-of-reading-cli-configuration-from-device>`__
  * `Reading of configuration from CLI network device - different scenarios <#reading-of-configuration-from-cli-network-device---different-scenarios>`__

This document provides developer-level details for the FRINX CLI southbound plugin, both for the framework itself as well as for the pluggable translation units.

Pre-requisite reading: - Honeycomb design documentation:

https://wiki.fd.io/view/Honeycomb

https://docs.fd.io/honeycomb/1.17.04/release-notes-aggregator/release_notes.html

CLI plugin available presentations:

https://frinxhelpdesk.atlassian.net/wiki/display/~mmarsalek/CLI+southbound+plugin+docs

`CLI plugin user guide <../../FRINX_Features_User_Guide/cli/cli-service-module.html>`_  

Building on honeycomb
---------------------

The essential idea behind the CLI southbound plugin comes from Honeycomb. Honeycomb defines, implements and uses the same pipeline and the same framework to handle data. The APIs, some implementations and also SPIs used in the CLI southbound plugin's translation layer come from Honeycomb. However, the CLI southbound plugin creates multiple instances of Honeycomb components and encapsulates them behind a mount point.

The following series of diagrams shows the evolution from Opendaylight to Honeycomb and back into Opendaylight as a CLI mountpoint:

High level Opendaylight overview with its concept of a Mountpoint:


.. image:: ODL.png
   :target: ODL.png
   :alt: ODL


High level Honeycomb overview:


.. image:: HC1.png
   :target: HC1.png
   :alt: HC


Honeycomb core (custom MD-SAL implementation) overview:


.. image:: HCsMdsal.png
   :target: HCsMdsal.png
   :alt: Honeycomb's core


How Honeycomb is encapsulated as a mount point in Opendaylight:


.. image:: cliMountpoint.png
   :target: cliMountpoint.png
   :alt: Honeycomb's core as mountpoint


Major components
----------------

The following diagram shows the major components of the CLI southbound plugin and their relationships:

.. image:: cliInComponents.png
   :target: cliInComponents.png
   :alt: CLI plugin components


Modules
-------

The following diagram shows project modules and their dependencies:

.. image:: projectComponents.png
   :target: projectComponents.png
   :alt: CLI plugin modules


Developing a device specific translation unit
---------------------------------------------

This section provides a tutorial for developing a device specific translation unit.

The easiest way how to develop a new transaction unit
is to copy existing one and change what you need to
make it work. E.g. if you are creating an interface
translation unit, the best way is to copy existing interface
translation unit for some other device, that is already
implemented. You can find existing units on github
`https://github.com/FRINXio/cli-units <https://github.com/FRINXio/cli-units>`_\ , `https://github.com/FRINXio/unitopo-units <https://github.com/FRINXio/unitopo-units>`_

What you need to change:


* .pom file of the unit

  * point to correct unit parent
  * dependencies
  * name of the unit should be in format ``<device>-<domain>-unit`` (e.g. ios-interface-unit, xr-acl-unit)

* package name should be in format ``io.frinx<cli|netconf>.``\ , device name and domain (eg. io.frinx.cli.unit.ios.interface)

What you need to add:


* add your unit as a dependency to artifacts/pom
* add your unit as a karaf feature

Installing to Opendaylight
~~~~~~~~~~~~~~~~~~~~~~~~~~

For how to run Opendaylight with the CLI southbound plugin, please refer to the `user guide <../../FRINX_Features_User_Guide/cli/cli-service-module.html>`_. To install a bundle with a new unit (e.g. previously built with maven) it is sufficient to run the following command in the karaf console:

.. code-block:: guess

   bundle:install -s file:///home/devel/ios-vrfs-unit/target/ios-vrfs-unit-1.0-SNAPSHOT.jar



Now the new unit should be reported by the CLI southbound plugin as being available. To verify its presence from RESTCONF, use the provided postman collection, *CLI registry* folder.

As a feature
++++++++++++

It is also possible to include this bundle into a karaf feature and make it install with that particular feature instead of using the *bundle:install* command.

Testing
~~~~~~~

Please see the `user guide <../../FRINX_Features_User_Guide/cli/cli-service-module.html>`_ for how to mount a CLI device. If there is a new unit installed in Opendaylight, it will be possible to use the new unit's YANG model and its handlers.

Choosing the right YANG models
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Before writing a custom YANG model for a unit, it is important to check whether such a model doesn't already exist. There are plenty of YANG models available, modeling many aspects of network device management. The biggest groups of models are:


* Openconfig https://github.com/openconfig/public/tree/master/release/models  
* IETF https://github.com/YangModels/yang/tree/master/standard/ietf  

It is usually wiser to choose an existing YANG model instead of developing a custom one. Also, it is very important to check for existing units already implemented for a device. If there are any, the best approach will most likely be to use YANG models from the same family as existing units use.

Implementing handlers
~~~~~~~~~~~~~~~~~~~~~

There are 2 types of handlers. Those which handle writes of configuration data and those which handle reads of operational data. The responsibility of a handler is just to transform between CLI commands and the YANG data. There is nothing more a handler needs to do. For an example, refer to the section discussing unit archetype.

Dependencies between writing handlers (writers)
+++++++++++++++++++++++++++++++++++++++++++++++

A writer may be registered with or without dependency on another writer.
The dependency between writers reflects the actual dependency between CLI
commands for a specific device.

The following sample shows a CLI translation unit with dependency between 2
writers. The unit is dedicated for interface configuration on a Cisco IOS
device.

.. code-block:: guess

   R2(config)#interface loopback 1
   R2(config-if)#ip address 10.0.0.1 255.255.255.255

As the example shows, the *ip address* command must be executed after the *interface*
command.

IOS CLI translation unit based on openconfig-interfaces YANG model
is `here <https://github.com/FRINXio/cli-units/tree/master/ios/interface/src/main/java/io/frinx/cli/unit/ios/ifc>`_. This CLI translation unit contains `InterfaceConfigWriter <https://github.com/FRINXio/cli-units/blob/master/ios/interface/src/main/java/io/frinx/cli/unit/ios/ifc/ifc/InterfaceConfigWriter.java>`_
translating the *interface* command and `Ipv4ConfigWriter <https://github.com/FRINXio/cli-units/blob/master/ios/interface/src/main/java/io/frinx/cli/unit/ios/ifc/subifc/Ipv4ConfigWriter.java>`_ translating
the *ip address* command. `IosInterfaceUnit <https://github.com/FRINXio/cli-units/blob/master/ios/interface/src/main/java/io/frinx/cli/unit/ios/ifc/IosInterfaceUnit.java>`_ contains registration of these
writers where dependency between writers is described:

.. code-block:: guess

   wRegistry.add(new GenericWriter<>(IIDs.IN_IN_CONFIG, new InterfaceConfigWriter(cli)));
   wRegistry.addAfter(new GenericWriter<>(SUBIFC_IPV4_CFG_ID, new Ipv4ConfigWriter(cli)), IIDs.IN_IN_CONFIG);

Registration of Ipv4ConfigWriter by using the *addAfter* method ensures that
the OpenConfig ip address data is translated after OpenConfig interface data.
That means CLI commands are executed in the desired order.

Writers can be registered by using methods:


* add - no dependency on another writer, execution order is not guaranteed
* addAfter - execute registered writer after dependency writer
* addBefore - execute registered writer before dependency writer

Implementing RPCs
~~~~~~~~~~~~~~~~~

An RPC handler is a special kind of handler, different to the data handlers. RPC handler can encapsulate any commands. The biggest difference is that any configuration processing in RPCs is not part of transactions, reconciliation etc.

Mounting and managing IOS devices from an application
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Besides mounting using Postman collections of RESTCONF calls (see the `user guide <../../FRINX_Features_User_Guide/cli/cli-service-module.html>`_\ ) it is also possible to manage an IOS device in a similar fashion from within an OpenDaylight application. It is however necessary to acquire an appropriate mountpoint instance from MD-SAL's mountpoint service.

To do so, first make sure to generate an appropriate Opendaylight application using the archetype.

Next make sure to add a Mountpoint service as a dependency of the application, so update your blueprint:

.. code-block:: guess

   <reference id="mountpointService"
              interface="org.opendaylight.mdsal.binding.api.MountPointService"/>



and add an argument to your component:

.. code-block:: guess

   <bean id="SOMEBEAN"
     class="PACKAGE.SOMEBEAN"
     init-method="init" destroy-method="close">
     <argument ref="dataBroker" />
     ...
     <argument ref="mountpointService"/>
   </bean>



Also add that argument to your constructor:

.. code-block:: guess

     final MountPointService mountpointService



So now to get a connected mountpoint from the service:

.. code-block:: guess

   Optional [MountPoint] mountPoint = a.getMountPoint(InstanceIdentifier.create(NetworkTopology.class) .child(Topology.class, new TopologyKey(new TopologyId("cli"))) .child(Node.class, new NodeKey(new NodeId("IOS1"))));

   if(mountPoint.isPresent()) { // Get DATA broker Optional<DataBroker> dataBroker = mountPoint.get().getService(DataBroker.class); // Get RPC service Optional<RpcService> rpcService = mountPoint.get().getService(RpcService.class);

       if(!dataBroker.isPresent()) {
           // This cannot happen with CLI mountpoints
           throw new IllegalArgumentException("Data broker not present");
       }


   }



And finally DataBroker service can be used to manage the device:

.. code-block:: guess

   ReadWriteTransaction readWriteTransaction = dataBroker.get().newReadWriteTransaction(); // Perform read // reading operational data straight from device CheckedFuture<Optional<Version>, ReadFailedException> read = readWriteTransaction.read(LogicalDatastoreType.OPERATIONAL, InstanceIdentifier.create(Version.class)); try { Version version = read.get().get(); } catch (InterruptedException | ExecutionException e) { e.printStackTrace(); }

   Futures.addCallback(readWriteTransaction.submit(), new FutureCallback<Void>() { @Override public void onSuccess(@Nullable Void result) { // Successfully invoked TX }

       @Override
       public void onFailure(Throwable t) {
           // TX failure
       }


   });



In this case *Version* operational data is being read from the device. In order to be able to do so, make sure to add a maven dependency on the IOS unit containing the appropriate YANG model.

Reading of CLI and device configuraiton
---------------------------------------

CLI readers maintain translation between device and yang models. We're sending read commands to the device and outputs are cached. This process is shown below.

Process of reading CLI configuration from device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The diagram below shows the general use of the process


.. image:: Process-of-reading-of-CLI-configuration-from-device.png
   :target: Process-of-reading-of-CLI-configuration-from-device.png
   :alt: Reading CLI conf from device


Reading of configuration from CLI network device - different scenarios
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The diagram below shows four specific scenarios:


#. Configuration is read using show running-config pattern for the first time
#. Another configuration is read using running-config pattern - cache can be used
#. BGP configuration/state is read using "show route bgp 100" - the running-config pattern is not used
#. BGP configuration/state is read using "show route bgp 100" again - cached can be used


.. image:: Reading-of-configuration-from-CLI-network-device-different-scenarios.png
   :target: Reading-of-configuration-from-CLI-network-device-different-scenarios.png
   :alt: Different scenarios

