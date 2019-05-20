
Questions & Answers: Deep dive into FRINX architecture
======================================================

List of content
---------------


* `What is the datastore used in FRINX ODL? <#what-is-the-datastore-used-in-frinx-odl>`__
* `Are service instances stored in the UniConfig layer of FRINX, or is each service application responsible for storing the appropriate information? <#is-it-true-that-service-instances-are-also-stored-in-the-uniconfig-layer-of-frinx->`__
* `When changing e.g. a translation unit of FRINX to the UNICONFIG layer is it possible to load the new models or add support for more features in run time? <#when-changing-e-g-a-translation-unit-of-frinx-to-the-uniconfig-layer-is-it-possible-to-load-the-new-models-or-add-support-for-more-features-in-run-time->`__
* `How does FRINX deal with model changes? <#how-does-frinx-deal-with-model-changes->`__
* `Does FRINX provide auto rollback on all affected devices, when a transaction fails on one device? <#does-frinx-provide-auto-rollback-on-all-affected-devices-when-a-transaction-fails-on-one-device->`__
* `When synchronizing configuration from a device into FRINX, is it possible to show the differences between the actual device configuration and the operational datastore? <#when-synchronizing-configuration-from-a-device-into-frinx-is-it-possible-to-show-the-differences-between-the-actual-device-configuration-and-the-operational-datastore->`__
* `Is any NETCONF device fully supported, or must OpenConfig be mapped to netconf as well? <#is-any-netconf-device-fully-supported-or-must-openconfig-be-mapped-to-netconf-as-well->`__
* `Are the libraries that are used to access the Config Data Store model driven? <#are-the-libraries-that-are-used-to-access-the-config-data-store-model-driven->`__
* `How would an access to the configuration data store look like in code using an eventual library? <#how-would-an-access-to-the-configuration-data-store-look-like-in-code-using-an-eventual-library->`__
* `Is it possible in FRINX to run transaction on two disjunct sets of devices simultaneously? <#is-it-possible-in-frinx-to-run-transaction-on-two-disjunct-sets-of-devices-simultaneously->`__
* `What access control measures does FRINX offer? <#what-access-control-measures-does-frinx-offer->`__
* `How does FRINX report problems with device interaction? <#how-does-frinx-report-problems-with-device-interaction->`__
* `How would you do a backup of the FRINX system? <#how-would-you-do-a-backup-of-the-frinx-system->`__
* `Is it possible to enforce policies over configuration changes? <#is-it-possible-to-enforce-policies-over-configuration-changes->`__
* `Does FRINX come with an integrated scheduler to automate repetetive tasks? <#does-frinx-come-with-an-integrated-scheduler-to-automate-repetetive-tasks->`__
* `In which languages are the libraries to access FRINX written? <#in-which-languages-are-the-libraries-to-access-frinx-written->`__
* `Are there ready made libraries for accessing the UniConfig layer? <#are-there-ready-made-libraries-for-accessing-the-uniconfig-layer->`__
* `Does FRINX/ODL detect if a cluster node is down on its own or does it rely on a high availability framework? <#does-frinx-odl-detect-if-a-cluster-node-is-down-on-its-own-or-does-it-rely-on-a-high-availability-framework->`__
* `How fast will FRINX integrate new ODL versions? <#how-fast-will-frinx-integrate-new-odl-versions->`__
* `Is it possible for FRINX to report problems to a network monitoring system? <#is-it-possible-for-frinx-to-report-problems-to-a-network-monitoring-system->`__
* `Is it possible to do logging additional to the logging provided by Karaf? <#is-it-possible-to-do-logging-additional-to-the-logging-provided-by-karaf->`__
* `How does the conductor server know, which APIs to contact on the FRINX/ODL distribution for each specific task? <#how-does-the-conductor-server-know-which-apis-to-contact-on-the-frinx-odl-distribution-for-each-specific-task->`__
* `Where do I find the status of the device and where do I find error messages, when mounting does not work? <#where-do-i-find-the-status-of-the-device-and-where-do-i-find-error-messages-when-mounting-does-not-work->`__
* `What does mounting exactly do? <#what-does-mounting-exactly-do>`__
* `Won’t rendering templates via conductor modify the configuration data store for the device in ODL? <#wont-rendering-templates-via-conductor-modify-the-configuration-data-store-for-the-device-in-odl>`__

What is the datastore used in FRINX ODL?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ODL uses a custom in memory database. It is part of MD-SAL and it is a very fast storage for YANG modeled data. It also supports persistance and clustered deployments.

Some documentation could be found here:   https://wiki.opendaylight.org/view/OpenDaylight_Controller:MD-SAL:Architecture:DOM_DataStore:Transactions  

and here: http://www.industry-academia.org/download/ODL-Yang-Data-Store.pdf  

Are service instances stored in the UniConfig layer of FRINX, or is each service application responsible for storing the appropriate information?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Only the „outputs“ of a service are stored and managed by uniconfig (e.g. service generates bgp config for 10 devices, which is pushed into uniconfig). The services themselves are responsible for managing their configuration/operational state. But both uniconfig and services rely on the same datastore to store configuration or operational data.

When changing e.g. a translation unit of FRINX or adding YANG models to the UNICONFIG layer, is it possible to load the new models or add support for more features in run time?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Adding a new unit for existing model in runtime is possible. Adding both a unit + models into the system is also possible at runtime. Currently, we only allow openconfig models to participate in uniconfig, so if there are some other models, configuration of the system has to be changed in uniconfig to allow those namespaces as well.

How does FRINX deal with model changes - e.g. when service models change and there are existing model instances. Does FRINX provide auto rollback on all affected devices, when a transaction fails on one device?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Yes, all onboarded devices have full rollback implemented. But it is also possible to disable auto-rollback in uniconfig, so that successfully configured devices will keep their configuration.

When synchronizing configuration from a device into FRINX, is it possible to show the differences between the actual device configuration and the operational datastore - evtl. before actually committing device sourced changes to the operational datastore
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To achieve this behavior follow these steps:
1. sync (update operational)
2. show diff
3. drop the changes from device by replacing operational with config

Is any NETCONF device fully supported, or must OpenConfig be mapped to netconf as well?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can either use the native device models (via UniConfig native) or use the existing translation units between OpenConfig and vendor models.

Are the libraries that are used to access the Config Data Store model driven?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ODL has a DataBroker interface and a concept of InstanceIdentifier. Those are the model driven APIs for data access.   

*More info:*
https://wiki.opendaylight.org/view/OpenDaylight_Controller:MD-SAL:Concepts

How would an access to the configuration data store look like in code using an eventual library?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A: Just to demonstrate API, in this example InterfaceConfigurations is read from CONF DS and put back to CONF DS.

..code_block:: abap

ReadWriteTransaction rwTx = dataBroker.newReadWriteTransaction();
InstanceIdentifier`<InterfaceConfigurations>` iid = InstanceIdentifier.create(InterfaceConfigurations.class);
InterfaceConfigurations ifcConfig = xrNodeReadTx.read(LogicalDatastoreType.CONFIGURATION, iid).checkedGet();
rwTx.put(LogicalDatastoreType.CONFIGURATION, iid, ifcConfig);
rwTx.submit();

Is it possible in FRINX to run transaction on two disjunct sets of devices simultaneously, or is the complete system locked down when committing a transaction to a set of devices?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

It is possible to have simultanious transactions if the transactions configure disjunct sets of devices.

What access control measures does FRINX offer?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

FRINX ODL supports local authentification, password authentification, public key authentification, Token authentification, RADIUS based authentification and subtree based authentification via AAA Shiro project.

How does FRINX report problems with device interaction?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If a CLI session gets interrupted or reset, FRINX ODL will try reestablish the connection. If a device can not be reached during a UniConfig transaction a timeout will occur and the cause for the transaction failure will be reported.

How would you do a backup of the FRINX system?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

FRINX distribution contains project called DAEXIM which allows you to export data in json format from data store and import it back.

Is it possible to enforce policies over configuration changes?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

All customer specific validations and policy enforcements can be implemented in layers above UniConfig

Does FRINX come with an integrated scheduler to automate repetetive tasks?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Scheduling of repetitive tasks is implemented in FRINX Conductor.

In which languages are the libraries to access FRINX written?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

FRINX ODL exposes RESTful API (RESTCONF) and NETCONF which can be used with Python or any other language that implements REST. FRINX ODL is written in JAVA and Kotlin.

Are there ready made libraries for accessing the UniConfig layer, or is it necessary to go through the RESTCONF API?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For communication from other process you may use RESTCONF or NETCONF. For communication in FRINX ODL you can write code in JAVA or Kotlin which can use data objects generated from YANG:

More info: https://wiki.opendaylight.org/view/YANG_Tools:YANG_to_Java_Mapping

Does FRINX/ODL detect if a cluster node is down on its own or does it rely on a high availability framework?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

FRINX ODL detects node failures in a cluster.

How fast will FRINX integrate new ODL versions?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

FRINX integrates major ODL versions with the focus on new customer features and we integrate bug fixes from ODL upstream to FRINX releases

Is it possible for FRINX to report problems to a network monitoring system?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A: FRINX ODL can send NETCONF notifications from web sockets on Northbound API

Is it possible to do logging additional to the logging provided by Karaf?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A: Yes. Each component supports different verbocity levels of logging (ERROR, WARN, INFO, DEBUG, TRACE).

How does the conductor server know, which APIs to contact on the FRINX/ODL distribution for each specific task?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

How are the request bodies that conductor receives mapped to requests against the FRINX/ODL API?

FRINX ODL APIs are documented in our Postman collection available with every FRINX release: https://frinxio.github.io/Frinx-docs/FRINX_ODL_Distribution/Carbon/API.html
We have implemented example workflows in Python which are part of FRINX MACHINE. Those example workflows implement FRINX ODL REST APIs: https://github.com/FRINXio/netinfra_utils/blob/simple/workers/mount_worker.py

Where do I find the status of the device and where do I find error messages, when mounting does not work?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To get status of the mounting process for all devices in the system, issue following request (it will show status as well as last connect attempt cause):  


* GET http://8181/restconf/operational/network-topology:network-topology

  **Authorization Basic**: YWRtaW46YWRtaW4=
  **Accept**: application/json
  **Content-Type**: application/json


* Note: VM-IP is the ip of VM running all the docker containers...
  or localhost if you execute the request directly in the VM  
* Each workflow contains a check to verify the device is mounted...
  there is a timeout of 20 * 5 seconds and if the device is not mounted in that time,
  the workflow fails  
* It should be visible from the Conductor UI which tasks failed and their output
  (with details why). If it’s not, some output/log might be omitted between the workflow,
  task and ODL. We can fix that.  
* You can also check the logs from Opendaylight...
  just go into container „odl“ and go into data/log folder,
  *where you can grep the log files for the device ID*

What does mounting exactly do?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Mounting a device serves the following purpose. First, open IO session (and keep it open). Then expose a mount-point in ODL (so that device can be managed over REST or internal API). Finally, collect any „units“ for that particular device and use the code when communicating with the device.

Won’t rendering templates via conductor modify the configuration data store for the device in ODL?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A: It interacts directly with the southbound device layer to push the configuration to the device.
If you would like UNICONFI to reflect change that was made to the device, execute a SYNC from network RPC: https://frinxio.github.io/Frinx-docs/FRINX_ODL_Distribution/Carbon/FRINX_Features_User_Guide/uniconfig/api_and_use_cases/api_and_use_cases.html#rpc-sync-from-network
