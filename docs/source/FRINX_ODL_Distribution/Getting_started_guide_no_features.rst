
Getting started with FRINX ODL
==============================

This guide explains how to run the FRINX UniConfig for the first time.
Content:

* `System requirements <#system-requirements>`__
* `Download the FRINX UniConfig distribution <#download-the-frinx-uniconfig-distribution>`__
* `Activate your FRINX UniConfig Distribution <#activate-your-frinx-uniconfig-distribution>`__
* `Connecting devices <#connecting-devices>`__



System requirements
-------------------

**RAM:** 8GB minimum; we recommend 16GB 
  
**Java:** FRINX distribution requires Java 8 (Openjdk 1.8.0-171 or newer)  
 
**Linux:** Supported  Linux distributions are Centos7, Ubuntu 16.04 and Ubuntu 18.04   

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

.. code-block:: bash

   kill -9 $(pgrep  -o -f  karaf)
   rm  -rf  data/ snapshots/ journal/

To stop FRINX UniConfig safely from within the karaf console, hold the 'CTRL' key and press the 'D' key.
For more info on operating karaf, see :doc:`running-frinx-odl-after-activation`

For non-standard setups, use this guide :doc:`Operations_Manual/running-frinx-odl-initial`


Connecting devices
------------------

When using Karaf to run FRINX UniConfig, the address used for this operation is localhost:8181.
After FRINX UniConfig initiation, make sure, you can reach your device through the network.
Either using VPN or when the device is located in the same network as your workstation, use the IP address of the device.

After completing steps listed above, you can proceed to the :doc:`API` documentation and start to configure your devices through FRINX UniConfig.
