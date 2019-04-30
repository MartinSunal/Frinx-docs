
NETCONF Mounting a device
=========================

*  `Step 1 Install features <#step-1-install-features>`__
*  `Step 2 Wait until controller-config is connected <#step-2-wait-until-controller-config-is-connected>`__
  *  `Step 3 Verify that connection was successful <#step-3-verify-that-connection-was-successful>`__

In this example we connect the controller to itself to verify that that NETCONF features were installed correctly. It is similar to loopback but done over a NETCONF session. After mounting itself, the YANG model of the Frinx ODL Distribution is exposed by RESTCONF and is an interesting point to begin exploring.

Step 1 Install features
-----------------------

This can be done using the Karaf CLI:

.. code-block:: guess

   feature:install odl-restconf
   feature:install odl-netconf-connector-all


Step 2 Wait until controller-config is connected
------------------------------------------------

Send a GET request to mount the controller using RESTCONF

http://localhost:8181/restconf/operational/opendaylight-inventory:nodes/node/controller-config

Step 3 Verify that connection was successful
--------------------------------------------

Examine the HTTP response - there should be a 200 status code and the text string

*netconf-node-inventory:connected":true*
