# NETCONF: Mounting a device

In this example we connect the controller to itself to verify that that NETCONF features were installed correctly. It is similar to loopback but done over a NETCONF session. After mounting itself, the YANG model of the Frinx ODL Distribution is exposed by RESTCONF and is an interesting point to begin exploring.

**Step 1 Install Features**

This can be done using either the Frinx ODL Distribution feature manager at

<http://localhost:8181/index.html#/featuremgr/index> or the Karaf CLI:

    feature:install odl-restconf 
    feature:install odl-netconf-connector-all
    

**Step 2 Wait Until controller-config Is Connected**

Send a GET request to mount the controller using RESTCONF

<http://localhost:8181/restconf/operational/opendaylight-inventory:nodes/node/controller-config>

**Step 3 Verify that connection was successful**

Examine the HTTP response - there should be a 200 status code and the text string

*netconf-node-inventory:connected":true*
