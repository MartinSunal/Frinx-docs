[Documentation main page](https://frinxio.github.io/Frinx-docs/)
[Operations Manual main page](https://frinxio.github.io/Frinx-docs/FRINX_ODL_Distribution/Carbon/operations_manual.html)
# NETCONF: Mounting a device

<!-- TOC START min:1 max:3 link:true update:true -->
- [NETCONF: Mounting a device](#netconf-mounting-a-device)
  - [Step 1 Install features](#step-1-install-features)
    - [To install for this session only](#to-install-for-this-session-only)
    - [For an install that persists in future session](#for-an-install-that-persists-in-future-session)
  - [Step 2 Wait until controller-config is connected](#step-2-wait-until-controller-config-is-connected)
  - [Step 3 Verify that connection was successful](#step-3-verify-that-connection-was-successful)

<!-- TOC END -->

In this example we connect the controller to itself to verify that that NETCONF features were installed correctly. It is similar to loopback but done over a NETCONF session. After mounting itself, the YANG model of the Frinx ODL Distribution is exposed by RESTCONF and is an interesting point to begin exploring.

## Step 1 Install features
### To install for this session only
This can be done using the Karaf CLI:

    feature:install odl-restconf
    feature:install odl-netconf-connector-all

### For an install that persists in future session
To load features consistently and in accordance with our Daexim edit the **org.apache.karaf.features.cfg** file within the etc directory of your Frinx ODL distribution main directory:

By setting the **odlFeaturesBoot** variable, features will be loaded at startup, every time Frinx ODL starts:

    odlFeaturesBoot=odl-restconf,odl-netconf-connector-all

## Step 2 Wait until controller-config is connected

Send a GET request to mount the controller using RESTCONF

<http://localhost:8181/restconf/operational/opendaylight-inventory:nodes/node/controller-config>

## Step 3 Verify that connection was successful

Examine the HTTP response - there should be a 200 status code and the text string

*netconf-node-inventory:connected":true*
