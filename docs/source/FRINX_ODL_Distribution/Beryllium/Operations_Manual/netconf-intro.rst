
NETCONF: Overview and use within the FRINX ODL Distribution
===========================================================

What is NETCONF?
----------------

NETCONF is an Internet Engineering Task Force (IETF) protocol used to “create, recover, update, and delete configurations of network devices”.

NETCONF operations are overlaid on the Remote Procedure Call (RPC) layer and may be described in either XML or JSON.

An introduction to the IEFT working group and a summary of relevant RFCs and drafts is available at

http://www.netconfcentral.org/ and the white paper included.

How does the Frinx ODL Distribution use NETCONF?
------------------------------------------------

The Frinx ODL Distribution's southbound APIs use a NETCONF connector to communicate with downstream devices. The northbound APIs expose the YANG models of connected devices. This makes it possible to examine the operational and config datastores and to configure devices using RPCs. RESTCONF maps a subset of these YANG models to a RESTful interface.

**NETCONF – features used by the Frinx ODL Distribution include:**  

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

