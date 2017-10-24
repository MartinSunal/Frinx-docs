[Documentation main page](https://frinxio.github.io/Frinx-docs/)
[FRINX Features Developer Guide main page](https://frinxio.github.io/Frinx-docs/FRINX_ODL_Distribution/developer_guide.html)
# Developer Guide: CLI

<!-- TOC START min:1 max:3 link:true update:true -->
- [Developer Guide: CLI](#developer-guide-cli)
  - [Building on honeycomb](#building-on-honeycomb)
  - [Major components](#major-components)
  - [Modules](#modules)
  - [Developing a device specific translation unit](#developing-a-device-specific-translation-unit)
    - [Using the archetype](#using-the-archetype)
    - [Installing to Opendaylight](#installing-to-opendaylight)
    - [Testing](#testing)
    - [Choosing the right YANG models](#choosing-the-right-yang-models)
    - [Implementing handlers](#implementing-handlers)
    - [Implementing RPCs](#implementing-rpcs)
    - [Mounting and managing IOS devices from an application](#mounting-and-managing-ios-devices-from-an-application)

<!-- TOC END -->

This document provides developer-level details for the FRINX CLI southbound plugin, both for the framework itself as well as for the pluggable translation units.

Pre-requisite reading: - Honeycomb design documentation:  
<https://wiki.fd.io/view/Honeycomb>  
<https://docs.fd.io/honeycomb/1.17.04/release-notes-aggregator/release_notes.html>  
CLI plugin available presentations:  
<https://frinxhelpdesk.atlassian.net/wiki/display/~mmarsalek/CLI+southbound+plugin+docs>  
[CLI plugin user guide][7]  


## Building on honeycomb

The essential idea behind the CLI southbound plugin comes from Honeycomb. Honeycomb defines, implements and uses the same pipeline and the same framework to handle data. The APIs, some implementations and also SPIs used in the CLI southbound plugin's translation layer come from Honeycomb. However, the CLI southbound plugin creates multiple instances of Honeycomb components and encapsulates them behind a mount point.

The following series of diagrams shows the evolution from Opendaylight to Honeycomb and back into Opendaylight as a CLI mountpoint:

High level Opendaylight overview with its concept of a Mountpoint:

![ODL][1]

High level Honeycomb overview:

![HC][2]

Honeycomb core (custom MD-SAL implementation) overview:

![Honeycomb's core][3]

How Honeycomb is encapsulated as a mount point in Opendaylight:

![Honeycomb's core as mountpoint][4]

## Major components

The following diagram shows the major components of the CLI southbound plugin and their relationships:  
![CLI plugin components][5]

## Modules

The following diagram shows project modules and their dependencies:  
![CLI plugin modules][6]

## Developing a device specific translation unit

This section provides a tutorial for developing a device specific translation unit.

### Using the archetype

There is an archetype to generate a skeleton of a translation unit. To generate a skeleton from the archetype, invoke the following command:

    mvn -X archetype:generate -DarchetypeGroupId=io.frinx.cli -DarchetypeArtifactId=unit-archetype -DarchetypeVersion=0.1.0-SNAPSHOT


You will be asked to fill in some parameters. As an example, values could be:

    Define value for property 'device-type': ios
    [INFO] Using property: device-version = *
    Define value for property 'group-id-prefix': io.frinx.cli
    Define value for property 'unit-domain': vrfs
    Define value for property 'groupId' io.frinx.cli.ios.vrfs: :
    Define value for property 'artifactId' ios-vrfs-unit: :
    Define value for property 'version' 1.0-SNAPSHOT: :
    [INFO] Using property: version = 1.0-SNAPSHOT
    Define value for property 'package' io.frinx.cli.ios.vrfs: :
    [INFO] Using property: cli-version = 0.1.0-SNAPSHOT
    Define value for property 'unit-name' iosvrfsUnit: :


This will generate just a single maven module called *ios-vrfs-unit*. The module contains:

*   sample YANG model
*   2 handlers handling CRUD operations for a node inside the YANG model
*   glue code to inject itself into the CLI southbound plugin framework

Running maven build should now succeed for the new unit. From this point, the unit can be extended in any way necessary.

### Installing to Opendaylight

For a unit generated from the archetype, you can directly install it into the already running Opendaylight CLI southbound plugin. For how to run Opendaylight with the CLI southbound plugin, please refer to the [user guide][7]. To install a bundle with a new unit (e.g. previously built with maven) it is sufficient to run the following command in the karaf console:

    bundle:install -s file:///home/devel/ios-vrfs-unit/target/ios-vrfs-unit-1.0-SNAPSHOT.jar


Now the new unit should be reported by the CLI southbound plugin as being available. To verify its presence from RESTCONF, use the provided postman collection, *CLI registry* folder.

#### As a feature

It is also possible to include this bundle into a karaf feature and make it install with that particular feature instead of using the *bundle:install* command.

### Testing

Please see the [user guide][7] for how to mount a CLI device. If there is a new unit installed in Opendaylight, it will be possible to use the new unit's YANG model and its handlers.

### Choosing the right YANG models

Before writing a custom YANG model for a unit, it is important to check whether such a model doesn't already exist. There are plenty of YANG models available, modeling many aspects of network device management. The biggest groups of models are:
- Openconfig <https://github.com/openconfig/public/tree/master/release/models>  
- IETF <https://github.com/YangModels/yang/tree/master/standard/ietf>  

It is usually wiser to choose an existing YANG model instead of developing a custom one. Also, it is very important to check for existing units already implemented for a device. If there are any, the best approach will most likely be to use YANG models from the same family as existing units use.

### Implementing handlers

There are 2 types of handlers. Those which handle writes of configuration data and those which handle reads of operational data. The responsibility of a handler is just to transform between CLI commands and the YANG data. There is nothing more a handler needs to do. For an example, refer to the section discussing unit archetype.

#### Dependencies between writing handlers (writers)

A writer may be registered with or without dependency on another writer.
The dependency between writers reflects actual dependency between CLI
commands for a specific device.

Following sample shows a CLI translation unit with dependency between 2
writers. The unit is dedicated for interface configuration on Cisco IOS
device.

```
R2(config)#interface loopback 1
R2(config-if)#ip address 10.0.0.1 255.255.255.255
```

As example shows *ip address* command must be executed after *interface*
command.

IOS CLI translation unit based on openconfig-interfaces YANG model
is [here][8]. This CLI translation unit contains [InterfaceConfigWriter][9]
translating *interface* command and [Ipv4ConfigWriter][10] translating
*ip address* command. [IosInterfaceUnit][11] contains registration of these
writers where dependency between writers is described:

```
wRegistry.add(new GenericWriter<>(IIDs.IN_IN_CONFIG, new InterfaceConfigWriter(cli)));
wRegistry.addAfter(new GenericWriter<>(SUBIFC_IPV4_CFG_ID, new Ipv4ConfigWriter(cli)), IIDs.IN_IN_CONFIG);
```

Registration of Ipv4ConfigWriter by using *addAfter* method ensures that
OpenConfig ip address data is translated after OpenConfig interface data.
That means CLI commands are executed in desired order.

Writers can be registered by using methods:

- add - no dependency on another writer, execution order is not guaranteed
- addAfter - execute registered writer after dependency writer
- addBefore - execute registered writer before dependency writer

### Implementing RPCs

An RPC handler is a special kind of handler, different to the data handlers. RPC handler can encapsulate any commands. The biggest difference is that any configuration processing in RPCs is not part of transactions, reconciliation etc.

### Mounting and managing IOS devices from an application

Besides mounting using Postman collections of RESTCONF calls (see the [user guide][7]) it is also possible to manage an IOS device in a similar fashion from within an OpenDaylight application. It is however necessary to acquire an appropriate mountpoint instance from MD-SAL's mountpoint service.

To do so, first make sure to generate an appropriate Opendaylight application using the archetype.

Next make sure to add a Mountpoint service as a dependency of the application, so update your blueprint:

    <reference id="mountpointService"
               interface="org.opendaylight.mdsal.binding.api.MountPointService"/>


and add an argument to your component:

    <bean id="SOMEBEAN"
      class="PACKAGE.SOMEBEAN"
      init-method="init" destroy-method="close">
      <argument ref="dataBroker" />
      ...
      <argument ref="mountpointService"/>
    </bean>


Also add that argument to your constructor:

      final MountPointService mountpointService


So now to get a connected mountpoint from the service:

    Optional [MountPoint] mountPoint = a.getMountPoint(InstanceIdentifier.create(NetworkTopology.class) .child(Topology.class, new TopologyKey(new TopologyId("cli"))) .child(Node.class, new NodeKey(new NodeId("IOS1"))));

    if(mountPoint.isPresent()) { // Get DATA broker Optional<DataBroker> dataBroker = mountPoint.get().getService(DataBroker.class); // Get RPC service Optional<RpcService> rpcService = mountPoint.get().getService(RpcService.class);

        if(!dataBroker.isPresent()) {
            // This cannot happen with CLI mountpoints
            throw new IllegalArgumentException("Data broker not present");
        }


    }


And finally DataBroker service can be used to manage the device:

    ReadWriteTransaction readWriteTransaction = dataBroker.get().newReadWriteTransaction(); // Perform read // reading operational data straight from device CheckedFuture<Optional<Version>, ReadFailedException> read = readWriteTransaction.read(LogicalDatastoreType.OPERATIONAL, InstanceIdentifier.create(Version.class)); try { Version version = read.get().get(); } catch (InterruptedException | ExecutionException e) { e.printStackTrace(); }

    Futures.addCallback(readWriteTransaction.submit(), new FutureCallback<Void>() { @Override public void onSuccess(@Nullable Void result) { // Successfully invoked TX }

        @Override
        public void onFailure(Throwable t) {
            // TX failure
        }


    });


In this case *Version* operational data is being read from the device. In order to be able to do so, make sure to add a maven dependency on the IOS unit containing the appropriate YANG model.

 [1]: ODL.png "ODL"
 [2]: HC1.png "HC"
 [3]: HCsMdsal.png "Honeycomb's core"
 [4]: cliMountpoint.png "Honeycomb's core as Mountpoint"
 [5]: cliInComponents.png "CLI plugin components"
 [6]: projectComponents.png "CLI plugin modules"
 [7]: ../../FRINX_Features_User_Guide/cli/cli-service-module.html
 [8]: https://github.com/FRINXio/cli-units/tree/master/ios/interface/src/main/java/io/frinx/cli/unit/ios/ifc
 [9]: https://github.com/FRINXio/cli-units/blob/master/ios/interface/src/main/java/io/frinx/cli/unit/ios/ifc/ifc/InterfaceConfigWriter.java
 [10]: https://github.com/FRINXio/cli-units/blob/master/ios/interface/src/main/java/io/frinx/cli/unit/ios/ifc/subifc/Ipv4ConfigWriter.java
 [11]: https://github.com/FRINXio/cli-units/blob/master/ios/interface/src/main/java/io/frinx/cli/unit/ios/ifc/IosInterfaceUnit.java