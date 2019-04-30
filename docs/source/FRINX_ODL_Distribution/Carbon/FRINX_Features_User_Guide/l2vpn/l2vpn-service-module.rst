
L2VPN Service Module User Guide
===============================

* `Usage - Setup <#usage-setup>`__

  * `FRINX ODL - Install features <#frinx-odl-install-features>`__
  * `Postman - Import collection <#postman-import-collection>`__

* `Introduction <#introduction>`__

  * `Problem definition and L2VPN <#problem-definition-and-l2vpn>`__
  * `Terminology <#terminology>`__
  * `L2VPN types <#l2vpn-types>`__

    * `VPWS <#vpws>`__

* `Usage Operations Guide <#usage-operations-guide>`__

  * `Set up an L2VPN connection <#set-up-an-l2vpn-connection>`__

    * `Establish a NETCONF connection <#establish-a-netconf-connection>`__
    * `Create a pseudo-wire (PW) template <#create-a-pseudo-wire-pw-template>`__
    * `Create the L2VPN instance <#create-the-l2vpn-instance>`__

  * `Delete the L2VPN connection <#delete-the-l2vpn-connection>`__
  * `FRINX L2VPN demo video (setup and deletion) <#frinx-l2vpn-demo-video-setup-and-deletion>`__
  * `Testing <#testing>`__

* `L2VPN Provider <#l2vpn-provider>`__

  * `Use Case Specification <#use-case-specification>`__
  * `Architecture <#architecture>`__

    * `API description <#api-description>`__

      * `ietf-l2vpn@2017-08-02.yang <#ietf-l2vpn@2017-08-02.yang>`__

  * `Network Element Plugin <#network-element-plugin>`__

    * `IOS XRv Network Element Plugin <#ios-xrv-network-element-plugin>`__
    * `Mock Network Element Plugin <#mock-network-element-plugin>`__

  * `Known Limitations <#known-limitations>`__

Usage - Setup
-------------

FRINX ODL - Install features
~~~~~~~~~~~~~~~~~~~~~~~~~~~~


1. First, :doc:`start FRINX ODL <../../Operations_Manual/running-frinx-odl-after-activation>`. 

   * Wait for 3 minutes to ensure the start up process is complete.  

2. Then, in the karaf terminal which will have started, install two features - RESTCONF and the l2vpn provider:  

.. code-block:: javascript

   feature:install odl-restconf frinx-l2vpn-iosxrv

**odl-restconf** enables us to communicate between FRINX ODL and the routers by using REST calls (which we issue with Postman).  

**frinx-l2vpn-iosxrv** is an L2VPN Provider with the IOS-XRv (Network Element Plugin) NEP and a NETCONF connector. This particular feature is specific for IOS-XRv devices.  

Postman - Import collection
~~~~~~~~~~~~~~~~~~~~~~~~~~~


#. To download and use FRINX pre-configured Postman REST calls with L2VPN - see :doc:`this page <../../API>`. 
#. Follow that guide to import the file ``postman_collection_L2VPN_IOS-XRv.json`` from the directory ``L2VPN Service Module``.
#. `Configure an environment in Postman :doc:'here <../../API.md>, where you set a value for ``odl_ip``.

Your system is now ready. To provision L2VPN see the `Usage - Operations Guide <#usage---operations-guide>`_ below.

Introduction
------------

The goal of this project is to automate provisioning of Layer 2 Virtual Private Networks (L2VPN) on Service Provider (SP) routers. 


* This is done by using the FRINX ODL controller which configures routers based on intent of the L2VPN service. 
* The FRINX ODL controller translates the L2VPN service abstraction to network element configuration.  


.. image:: l2vpn_service.png
   :target: l2vpn_service.png
   :alt: L2VPN Service


Problem definition and L2VPN
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Consider the scenario where a company needs to reconnect multiple sites with each other via an SP which provides L2 services to the company. 


* The company's sites needs to see each other as directly connected on L3. 
* L2VPN offers a solution for those requirements.

The company has two different sites and they are both connected to the Service Provider using an L2 connection. They need to interconnect two of their sites. 


.. image:: problem.png
   :target: problem.png
   :alt: Two company's sites connected to SP


In this case L2VPN provides site-to-site connectivity and the SP network behaves as a wire between the company’s sites. The company’s routes are exchanged via the SP network. 


.. image:: problem_solution.png
   :target: problem_solution.png
   :alt: Solution with L2VPN between sites.


Terminology
~~~~~~~~~~~

These terms are usually used in the L2VPN domain:


* **Customer Edge (CE)** device – router at customer site connected to SP
* **Provider Edge (PE)** device – router at the edge of the SP network which provides connectivity for CE
* **Provider (P)** device – core router on the SP network providing connectivity among PE routers


.. image:: terminology.png
   :target: terminology.png
   :alt: Terminology in picture


L2VPN types
~~~~~~~~~~~

There are two main types of L2VPN:


* point-to-point
* point-to-multipoint

These types have many implementations. The FRINX ODL distribution supports Virtual Private Wire Service (VPWS) implementation.

VPWS
++++

VPWS (Virtual Private Wire Service) is the simplest form for enabling Ethernet services over MPLS. 


* Also known as ETHoMPLS (Ethernet over MPLS), or VLL (Virtual Leased Line). 
* VPWS is point-to-point L2VPN which usually uses MPLS in core networks for signaling and creates pseudo-wires on PE routers for separation of L2 connections. 
* L2 connections are identified by interface or VLAN. 
* The picture below shows an MPLS core network with pseudo-wires on PEs for each VPN which are identified by VLAN.


.. image:: vpws_topology.png
   :target: vpws_topology.png
   :alt: VPWS example


Usage - Operations Guide
------------------------

To import the necessary Postman collection file see the section `Postman - Import collection <#postman---import-collection>`_ at the top of this page.  

That file contains several REST calls for establishing a NETCONF connection and creating or deleting L2VPN instances, for which we provide guidance below:

Set up an L2VPN connection
~~~~~~~~~~~~~~~~~~~~~~~~~~

Three steps are required to create an l2vpn connection between two routers (we perform these steps in our `video <https://youtu.be/UkHj9OgHHyo>`_ which you can use a reference):  

1. Establish a NETCONF connection
+++++++++++++++++++++++++++++++++

This is between FRINX ODL and each of the two routers which we'll use for the L2VPN. 


*  Use Postman REST calls: ``NETCONF connection/connect pe1`` (for router 1) and ``NETCONF connection/connect pe2`` (for router 2):  

*  First edit the body of the call ``NETCONF connection/connect pe1`` according to your setup for router 1. Only the fields with comments below should be edited:  

.. code-block:: javascript

   {
     "node": [
       {
         "node-id": "pe1",
         "netconf-node-topology:host": "192.168.1.211",//Edit this according to your setup
         "netconf-node-topology:port": 830,
         "netconf-node-topology:keepalive-delay": 0,
         "netconf-node-topology:tcp-only": false,
         "netconf-node-topology:username": "cisco",//Edit this according to your setup
         "netconf-node-topology:password": "cisco"//Edit this according to your setup
       }
     ]
   }


.. image:: connect-pe1.PNG
   :target: connect-pe1.PNG
   :alt: connect pe1



*  Issue the call by hitting **Send**. You should receive the Response: Status **201 Created**

*  Now configure the call ``NETCONF connection/connect pe2`` in the same way, but this time editing the body according to your setup for router 2  

*  Again, issue the call by hitting **Send**, ensuring you receive the Response: Status **201 Created**

*  It can take a few minutes before the connections are formed. We can check if they're ready by issuing the call ``NETCONF connection/topology-netconf OPER``


  * You should receive the Response: Status **200 OK**
  * When you scroll through the Response body you should see a list **"available-capability"** for both **"node-id": "pe1"** and **"node-id": "pe2"**. If these are not listed, wait another minute and issue the call again.

2. Create a pseudo-wire (PW) template
+++++++++++++++++++++++++++++++++++++

This will be used in the next step when we create the L2VPN instance.  


* Use the Postman REST call: ``L2VPN Service/create PW template PW1``. You don't need to change any of the fields of the call body. You can change **name** if you wish.

.. code-block:: javascript

   {  
     "pw-template":[  
       {  
         "name":"PW1",
         "cw-negotiation":"preferred",
         "encapsulation":"mpls"
       }
     ]
   }


.. image:: create-pw-template.PNG
   :target: create-pw-template.PNG
   :alt: create pw template



* Issue the call by hitting **Send**. You should receive the Response: Status **201 Created**

3. Create the L2VPN instance
++++++++++++++++++++++++++++

Use the Postman REST call: ``L2VPN Service/create l2vpn instance ce1-ce2_vlan3001``  


* Edit the call body according to your setup. Only the fields with comments below should be edited:

.. code-block:: javascript

   {  
     "l2vpn-instance":[  
       {  
         "name":"ce1-ce2_vlan3001",
         "type":"vpws-instance-type",
         "service-type":"Ethernet",
         "signaling-type":"ldp-signaling",
         "tenant-id":"frinx",
         "pw":[
           {
             "name":"pe1_pw999_vlan3001",
             "template":"PW1",//If you edited the name in step 2. then use the same name here
             "peer-ip":"172.16.2.2",//Edit to the IP of the interface on router 2
             "pw-id":999,
             "request-vlanid":3001
           },
           {
             "name":"pe2_pw999_vlan3001",
             "template":"PW1",//If you edited the name in step 2. then use the same name here
             "peer-ip":"172.16.1.2",//Edit to the IP of the interface on router 1
             "pw-id":999,
             "request-vlanid":3001
           }
         ],
         "endpoint":[
           {
             "name":"ce1",
             "pe-node-id":"pe1",
             "pe-2-ce-tp-id":"GigabitEthernet0/0/0/0",
             "pw":[
               {
                 "name":"pe1_pw999_vlan3001"
               }
             ]
           },
           {
             "name":"ce2",
             "pe-node-id":"pe2",
             "pe-2-ce-tp-id":"GigabitEthernet0/0/0/0",
             "pw":[
               {
                 "name":"pe2_pw999_vlan3001"
               }
             ]
           }
         ]
       }
     ]
   }


.. image:: create-l2vpn-instance.PNG
   :target: create-l2vpn-instance.PNG
   :alt: create l2vpn instance



*  Issue the call by hitting **Send**. You should receive the Response: Status **201 Created**

*  We now need to commit by RPC: Issue the call ``L2VPN Service/RPC commit-l2vpn``. In the Response body you should receive "status": "complete". This shows the setup has been competed successfully.

Delete the L2VPN connection
+++++++++++++++++++++++++++

If you want to remove the L2VPN connection:


1. Delete the pseudo-wire template by:

   * using the Postman REST call: ``L2VPN Service/delete PW template PW1``. There is no body to the call.  
   * commit by RPC: Issue the Postman REST call: ``L2VPN Service/RPC commit-l2vpn``. There is no body to the call.  

     * In the Response body you should receive "status": "complete". This shows the deletion has been competed successfully.

2. Delete the l2vpn instance by using the Postman REST call: ``L2VPN Service/delete l2vpn-instance ce1-ce2_vlan3001``. There is no body to the call. 

   * commit by RPC: Issue the Postman REST call: ``L2VPN Service/RPC commit-l2vpn``. There is no body to the call.  

     * In the Response body you should receive "status": "complete". This shows the deletion has been competed successfully.

FRINX L2VPN demo video (setup and deletion)
+++++++++++++++++++++++++++++++++++++++++++

See our `video <https://youtu.be/UkHj9OgHHyo>`_  

Testing
~~~~~~~

We also provide a feature which can be used for testing the l2vpn feature:

**Karaf installation:**

.. code-block:: javascript

   feature:install frinx-l2vpn-testing


**Description:**

Installs L2VPN Provider with Mock NEP and RESTCONF. This feature can be used for testing and demonstration purposes where real PE devices are not available.

L2VPN Provider
--------------

L2VPN Provider is an implementation which automatically provisions L2VPN on PE routers based on intended L2VPN service.   

It exposes a domain specific API for L2VPN manipulation and declarative configuration “what vs how”.  


* L2VPN Provider supports *network wide transactions* which are transactions on top of multiple devices. 
* *Rollback* of a network wide transaction means rollback of configuration on each device which was a part of the conifiguration. 
* *The rollback of a network wide transaction is done automatically* if there is failed configuration on at least one device.

Use Case Specification
----------------------

L2VPN Provider can be used on a network where:


* VPWS L2VPN is needed
* VLAN is used for pseudo-wire selection
* MPLS encapsulation is used in SP core


.. image:: use-case.png
   :target: use-case.png
   :alt: Use case example


L2VPN Provider works only with devices which have these capabilities:


.. raw:: html

   <table>
     <thead>
       <tr>
         <th>
           Name
         </th>
         <th>
           Revision
         </th>
       </tr>
     </thead>
     <tbody>
       <tr>
         <td>
           Cisco-IOS-XR-l2-eth-infra-cfg
         </td>
         <td>
           2015-11-09
         </td>
       </tr>
       <tr>
         <td>
           Cisco-IOS-XR-ifmgr-cfg
         </td>
         <td>
           2015-07-30
         </td>
       </tr>
       <tr>
         <td>
           Cisco-IOS-XR-l2vpn-cfg
         </td>
         <td>
           2015-11-09
         </td>
       </tr>
       <tr>
         <td>
           rollback-on-error
         </td>
         <td>
         </td>
       </tr>
     </tbody>
   </table>


The capabilities are sent from XR to ODL automatically during device connection via NETCONF.  

You can see the NETCONF capabilities under each node by calling (replacing odl_ip with the IP of the system on which you're running FRINX ODL):

.. code-block:: javascript

   GET http://odl_ip:8181/restconf/operational/network-topology:network-topology/topology/topology-netconf

A list of PE nodes can be obtained from (replacing odl_ip with the IP of the system on which you're running FRINX ODL):

.. code-block:: javascript

   GET http://odl_ip:8181/restconf/operational/network-topology:network-topology/topology/l2vpn-provider-edge-topology

Architecture
~~~~~~~~~~~~

L2VPN Provider is composed of multiple components. The high level architecture is shown in the picture below.


.. image:: architecture.png
   :target: architecture.png
   :alt: Architecture


An external application modifies **ietf-l2vpn** in CONF DS. L2VPN can be configured on nodes which are read from **l2vpn-provider-edge-topology**.  


* When all changes are done, the external application calls RPC *commit-l2vpn*. 
* The RPC reads **ietf-l2vpn** from CONF DS (the intended state) and from OPER DS (the actual state). 
* A diff is created based on intended vs actual state. 
* This diff is configured inside network wide transaction on the necessary PE routers by using particular Network Element Plugins. 
* If configuration of routers is successful then a new **ietf-l2vpn** is stored to OPER DS and RPC output is returned with status "complete". 
* If configuration fails on one of the devices, the **rollback** of the network wide transaction starts and if the rollback is successful then RPC output has status "commit-failed-rollback-complete", otherwise the status is "inconsistent". 
* The architecture can be extended very easily because Network Element Plugin needs to implement only NEP SPI, rollback, and network element registration. 
* Note that IOS NEP from the image above is not yet implemented.

As stated earlier, NEP registers network elements to L2VPN Provider. L2VPN Provider stores network elements as nodes to abstract topology **l2vpn-provider-edge-topology** and this topology is a source of nodes which can be used for L2VPN configuration.

API description
+++++++++++++++

The API is described using YANG modules. 


* An external application can consume the API via RESTCONF, NETCONF, or JAVA. 
* The L2VPN service module provides domain specific abstraction where the abstraction describes attributes of VPNs and sites instead of configuration of network elements. 
* The FRINX ODL Distribution translates the abstraction to network element configuration.

ietf-l2vpn@2017-08-02.yang
++++++++++++++++++++++++++


* The original YANG is from `RFC draft - YANG Data Model for MPLS-based L2VPN <https://tools.ietf.org/html/draft-ietf-bess-l2vpn-yang-05>`_. 
* This YANG module is modified due to compatibility with OpenDaylight and is extended with L2VPN Provider elements - see the modified YANG module :download:`ietf-l2vpn@2017-08-02.yang <ietf-l2vpn@2017-08-02.yang>`.

The YANG module contains 2 root statements and one RPC:


* **container l2vpn** – represents intended state stored in CONF DS and actual state stored in OPER DS
* **container l2vpn-state** – not used in current implementation
* **rpc commit-l2vpn** – configures intent of L2VPN service. The output of RPC is the result of service configuration.

Network Element Plugin
~~~~~~~~~~~~~~~~~~~~~~

Network Element Plugin (NEP) is a unit which implements SPI from the L2VPN Provider. This NEP is device API specific and is responsible for:


* Announcement of discovered device (PE) to the L2VPN Provider
* Translation between SPI Data Transfer Objects (DTO) and device configuration
* Rollback of configuration on a device

IOS-XRv Network Element Plugin
++++++++++++++++++++++++++++++

This plugin configures L2VPN on IOS-XRv using NETCONF. 


* It listens on **topology-netconf** and announces PE capable devices to the L2VPN Provider. 
* Rollback on a device is done automatically using the "Rollback-on-Error" capability.


.. image:: nep_ios-xrv.png
   :target: nep_ios-xrv.png
   :alt: IOS-XRv NEP



* IOS-XRv NEP listens on nodes in **topology-netconf**. 
* When a new IOS-XRv device is connected to FRINX ODL it appears as a new node in **topology-netconf** and IOS-XRv registers that node as PE to L2VPN Provider. 
* If L2VPN Provider calls SPI in order to configure PEs via the IOS-XRv NEP, NETCONF is used for device configuration.

Here is an example of L2VPN configuration on IOS-XRv ``(parameters encapsulated in ** are specific for VPN or site)``:


.. raw:: html

   <pre>interface **GigabitEthernet0/0/0/0** l2transport
    no shutdown
   !

   interface **GigabitEthernet0/0/0/0.3001** l2transport
    encapsulation dot1q **3001**
    rewrite ingress tag pop 1 symmetric
    no shutdown
   !

   l2vpn
    pw-class **PW1**
     encapsulation mpls
      control-word
     !
    !
    xconnect group **frinx**
     p2p **ce1**
      interface **GigabitEthernet0/0/0/0.3001**
      neighbor ipv4 **172.16.2.2** pw-id **999**
       pw-class **PW1**
      !
     !
    !
   !
   </pre>


Mock Network Element Plugin
+++++++++++++++++++++++++++

The purpose of this plugin is to mock functionality of the Network Element Plugin. It is mainly use for testing when you do not need to connect real devices. 


.. image:: nep_mock.png
   :target: nep_mock.png
   :alt: Mock NEP



* The Mock NEP listens on nodes from **mock-pe-topology**. 
* When a node is created, the NEP registers this node as a PE node to the L2VPN Provider. 
* When the L2VPN Provider calls the SPI which Mocks NEP implements, intead of configuration of real devices, the SPI DTOs are logged.

Known Limitations
~~~~~~~~~~~~~~~~~


* Implementation of L2VPN provider does not support all statements in :download:`ietf-l2vpn@2017-08-02.yang <ietf-l2vpn@2017-08-02.yang>`.

  * All supported elements are listed in the Postman collection. 

* L2VPN Provider does not support reconciliation, therefore only L2VPNs created via L2VPN Provider are visible through the API.

Other limitations:


* Only MPLS encapsulation is supported
* Only VLAN can be used between CE and PE for pseudo-wire selection
* Pre-configured MPLS among PEs must exist

.. list-table::
   :header-rows: 1

   * - Feature Guide
     - 
     - 
   * - 
     - FRINX 3.1.0
     - Removed all elements from yang which are not supported in implementation
   * - Feature introduced in
     - FRINX 2.3.1
     - VPN service module implementation with support for L2VPN and IOS XR (Version 6.1.2) NEP via NETCONF

