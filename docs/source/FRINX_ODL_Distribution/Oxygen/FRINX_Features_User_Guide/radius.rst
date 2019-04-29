
RADIUS
======

Introduction
------------

**RADIUS - How FRINX contributes to OpenDaylight authentication**

Within OpenDaylight, authentication and authorization is handled by the AAA feature, of which Apache Shiro is the core component. Apache Shiro can itself cooperate with third party authentication and authorization tools. However, in its current implementation within OpenDaylight it supports only LDAP.

FRINX has extended the functionality of the AAA feature by adding Apache Shiro interaction with RADIUS. RADIUS is a network protocol which authenticates a user who connects and uses a network service. The user sends a request to a Network Access Server (NAS) to gain access to a particular network resource using access credentials.

The NAS sends a RADIUS *AccessRequest* message to the RADIUS server, in our case freeRADIUS. The RADIUS server then checks that the information is correct using authentication schemes; in the case of the FRINX distribution, PAP is used. It can check the user information against an external resource such as LDAP in order to verify the user's information.

The response from the Radius server can be either: Access Accept - The user is granted access. This information may be stored locally on the RADIUS server, or may be looked up in an external source such as LDAP. Access Reject - The user is unconditionally denied access to all requested network resources. Reasons may include failure to provide proof of identification or an unknown or inactive user account.

Quick Start
^^^^^^^^^^^

Within the FRINX distribution, the AAA feature extends authentication capabilities via the RADIUS protocol. In order to be able to authenticate the user it is necessary to start freeRADIUS, which acts as a RADIUS server.

To download the freeRADIUS image:

.. code-block:: bash

   docker pull docker.io/unico/freeradius

To run the freeRADIUS server:

.. code-block:: bash

   docker run -it docker.io/unico/freeradius

RADIUS configuration
^^^^^^^^^^^^^^^^^^^^

Within your FRINX distribution directory structure, go to the /etc directory and edit the *shiro.ini* file. Under "[main]" enter the following four lines of text:

.. code-block:: guess

   radiusRealm = org.opendaylight.aaa.shiro.realm.RadiusRealm securityManager.realms = $radiusRealm
   radiusRealm.serverIp = 172.17.0.2
   radiusRealm.sharedSecret = dGVzdGluZzEyMw==


The final two lines set the RADIUS server ip address and shared secret string. The shared secret string is Base64 encoded.

Prepare the environment
^^^^^^^^^^^^^^^^^^^^^^^

Start the controller Karaf container:

.. code-block:: bash

   bin/karaf frinx.createtoken <user token>

Check the installed bundle for RADIUS authentication. It must be in ACTIVE state:

.. code-block:: bash

   la | grep -i aaa-authn-radius

Open the browser and type the following link: http://localhost:8181/index.html#/login Enter the credentials: username: admin password: password

If the credentials are correct, the RADIUS server sends *AccessAccept* and the user can access protected resources.

Change RADIUS credentials
^^^^^^^^^^^^^^^^^^^^^^^^^

Start the freeRADIUS docker image:

.. code-block:: bash

   docker exec -it <CONTAINER_ID> /bin/bash

Go to /etc/freeradius and open the users file:

.. code-block:: bash

   vi /etc/freeradius/users

Check the line where the default user is defined e.g.:

admin Cleartext-Password := "password"

Change the username and password and save the file. Next, exit from the container and restart the docker image:

.. code-block:: bash

   docker stop <CONTAINER_ID>   
   docker start <CONTAINER_ID>

What is Apache Shiro?
^^^^^^^^^^^^^^^^^^^^^

A Java Security Framework that performs authentication, authorization, cryptography, and session management and can be used to secure any application - from command line applications through mobile applications to the largest Web and enterprise applications. Its benefits are:
• Cryptography - Keeping data secure using a cryptographic algorithm
• Session management - Managing user-specific sessions, even in non-Web or EJB applications
• Easy to use - It has a long history (5 years), previously known as the JSecurity project. Detailed documentation is available and the API is easy to use
• It can use many connectors, for example LDAP, RADIUS, TACAS, Diameter

.. list-table::
   :header-rows: 1

   * - Feature Guide
     - 
     - 
   * - Feature introduced in
     - FRINX 2.2.1
     - RADIUS module

