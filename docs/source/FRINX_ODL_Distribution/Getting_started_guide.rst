
Getting started with FRINX UniConfig
====================================

Content:

* `Initial information <#initial-information>`__
* `Download the FRINX UniConfig distribution <#download-the-frinx-uniconfig-distribution>`__
* `Activate your FRINX UniConfig Distribution <#activate-your-frinx-uniconfig-distribution>`__
* `Non-standard setups <#nonstandard-setups>`__

  * `Activating the FRINX UniConfig Distribution behind a proxy <#activating-the-frinx-uniconfig-distribution-behind-a-proxy>`__
  * `Activating the FRINX UniConfig Distribution on a server without Internet access <#activating-the-frinx-uniconfig-distribution-on-a-server-without-internet-access>`__
  * `Activating the FRINX UniConfig Distribution with token read from the frinx.license.cfg file <#activating-the-frinx-uniconfig-distribution-with-token-read-from-the-frinx-license-cfg-file>`__

* `UniConfig basic principles <#uniconfig-basic-principles>`__
* `Install features <#install-features>`__

  * `Features for UniConfig native <#features-for-uniconfig-native-features>`__
  * `Features for CLI <#features-for-cli>`__
  * `Features for NETCONF <#features-for-netconf>`__

* Connecting devices <#connecting-devices>`__

Initial information
-------------------

This guide explains how to run the distribution for the first time.

**System requirements**  

**RAM:** 8GB minimum; we recommend 16GB.
**Java:** FRINX distribution requires Java 8 (Openjdk 1.8.0-171 or newer).
**Linux:** Supported  Linux distributions are Centos7, Ubuntu 16.04 and Ubuntu 18.04.  

*Note: Unless stated otherwise, this documentation assumes you are using Linux.*  

**To install Java:**

Ubuntu: Run a terminal and insert a command below:

.. code-block:: guess

   sudo apt-get install openjdk-8-jre


CentOS: Run a terminal and insert a command below:

.. code-block:: guess

   sudo yum install java-1.8.0-openjdk

Download the FRINX UniConfig distribution
-----------------------------------------

Please click on the following link to download a zip archive of the latest Oxygen FRINX UniConfig Distribution:

**Oxygen**: `distribution-karaf-4.2.0.frinx.zip <https://license.frinx.io/download/distribution-karaf-4.2.0.frinx.zip>`__

By downloading the file you accept the FRINX software agreement: `EULA <7793505-v7-Frinx-ODL-Distribution-Software-End-User-License-Agreement.pdf>`__

Activate your FRINX UniConfig Distribution
------------------------------------------

To activate your installation, unzip the file and open the directory.
Enter the following commands in a terminal to start and activate FRINX UniConfig.  

**Note**: The token is unique to your user account on frinx.io and cannot be shared with other users.
It can be found `here <https://frinx.io/my-licenses-information>`__ (you need to be logged in frinx.io to view your token)

.. code-block:: guess

   ./bin/karaf frinx.createtoken [frinx-license_secret-token]

**Note**: In the event of copying the command shown above into the terminal, insert your unique token in place of [frinx-licence_secret-token]. Do not insert token number into the square brackets.

FRINX UniConfig needs approximately **3 minutes** to startup and shutdown.  

To maintain system integrity, please do not interrupt the startup by shutting down running processes within this time.  

In the event of interruption, the initial state can be restored by entering the following commands from a terminal within your FRINX UniConfig main directory.  

**First** command forcibly kills the FRINX UniConfig karaf process
**Second** command cleans certain directories:

.. code-block:: guess

   kill -9 $(pgrep  -o -f  karaf)
   rm  -rf  data/ snapshots/ journal/

To stop FRINX UniConfig safely from within the karaf console, hold the 'CTRL' key and press the 'D' key.
For more info on operating karaf, see :doc:`running-frinx-odl-after-activation`

Non-standard setups
-------------------

Activating the FRINX UniConfig Distribution behind a proxy
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Please set up java system properties as described here: https://docs.oracle.com/javase/6/docs/technotes/guides/net/proxies.html

This means running karaf with something like this:

.. code-block:: guess

   JAVA_OPTS="-Dhttp.proxyHost=10.0.0.100 -Dhttp.proxyPort=8800" bin/karaf frinx.createtoken



Activating the FRINX UniConfig Distribution on a server without Internet access
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Let's call the connected computer ONLINE and the one where you want to run karaf OFFLINE.

.. code-block:: guess

   OFFLINE# TOKEN="insert your token here"
   OFFLINE# KARAF_HOME="insert path to karaf"
   OFFLINE# echo "token=$TOKEN";
   $KARAF_HOME/etc/frinx.license.cfg



Generate fingerprint json to a local file:

.. code-block:: guess

   OFFLINE# $KARAF_HOME/bin/karaf frinx.fingerprint > fingerprint.txt



Now, copy fingerprint.txt to the ONLINE machine:

.. code-block:: guess

    ONLINE# curl https://license.frinx.io/api/v1/obtain-license -d "@fingerprint.txt"  -H 'Content-Type: application/json' -X PUT > frinx.license.cfg



Copy frinx.license.cfg back to OFFLINE machine, replacing the file in karaf's etc folder. You will be able to start karaf normally:

.. code-block:: guess

   OFFLINE# $KARAF_HOME/bin/karaf


Activating the FRINX UniConfig Distribution with token read from the frinx.license.cfg file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the event of activating FRINX UniConfig multiple times it might be tedious to repeatedly provide your license key.  

To avoid that you can just create file frinx.license.cfg at $HOME/.local/share/frinx_odl/frinx.license.cfg with the content:

.. code-block:: guess

   token=<YOUR SECRET TOKEN>


For example token=fasf6a4f664f6sa4f64asf. This will provide your FRINX UniConfig your token whenever needed. After you created the file you may run FRINX UniConfig with clean install just with:

.. code-block:: guess

   ./bin/karaf

UniConfig basic principles
--------------------------

The purpose of UniConfigis to manage the intent(desired configuration)of physical and virtual networking devices through a single network API. In addition, UniConfigenables device and network wide transactions so that the network will always remain in a well-defined state without leftovers from failed  configuration attempts. UniConfigis delivered as an applicationinthe FRINXOpenDaylight(ODL)Distributionand as a standalone application. UniConfigenables users to communicate with their network infrastructure via three different options:

1)Via unstructured data through CLI
2)Via OpenConfig API withthe help of our open source device library
3)Via vendor YANG models native to the connected devices

Option 1) gives users similar capabilities like access through Ansibleor similar toolsand allows to pass strings to the device and receive strings from the devicein a programmatic way. UniConfig provides the mechanism to authenticate and provide a channel to send and receive data but does not interpret the data.That is left for the user application to do.

Option 2) provides users with an OpenConfig API that is translated into device specific CLI or YANG models. Thisrequires “translation units”to be installedfor the devices under control. FRINX provides an open source device library that includes many devices from widely deployed network vendors.

Option 3) also called “UniConfig native”,provides the ability to configure devices with any YANGmodelthat is supported by the device. After mounting a device, UniConfig native maps the vendor models into its UniConfig data store and provides stateful configuration capabilitiesto applications and users

.. image:: FRINX_UniConfig_solution.jpg
   :target: FRINX_UniConfig_solution.jpg
   :alt: UniConfig solution

Install features
----------------

To initiate FRINX UniConfig running on Karaf, use this command:

.. code-block:: guess

   ./bin/karaf

Features for UniConfig native
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Install the features necessary to use UniConfig-native with command below:

.. code-block:: guess

   frinx-user@root>feature:install frinx-UniConfig-native frinx-unified-topology odl-netconf-topology

Features for CLI
~~~~~~~~~~~~~~~~

.. code-block:: guess

   feature:install cli-southbound-all-units odl-restconf odl-netconf-connector-all unified-topology-all-units frinx-installer-backend uniconfig-node-manager

Features for NETCONF
~~~~~~~~~~~~~~~~~~~~

.. code-block:: guess

   feature:install odl-netconf-connector-all

Connecting devices
------------------

When using Karaf to run FRINX UniConfig, the address used for this operation is localhost:8181.
After FRINX UniConfig initiation, make sure, you can reach your device through the network.
Either using VPN or when the device is located in the same network as your workstation, use the IP address of the device.

After completing steps listed above, you can proceed to the :doc:`API` documentation and start to configure your devices through FRINX UniConfig.
