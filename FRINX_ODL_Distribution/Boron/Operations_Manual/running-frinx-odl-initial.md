[Documentation main page](https://frinxio.github.io/Frinx-docs/)
[Operations Manual main page](https://frinxio.github.io/Frinx-docs/FRINX_ODL_Distribution/Boron/operations_manual.html)
<!-- TOC -->

- [Activating the FRINX ODL Distribution](#activating-the-frinx-odl-distribution)
    - [Download the FRINX ODL Distribution](#download-the-frinx-odl-distribution)
    - [Activate your FRINX ODL Distribution](#activate-your-frinx-odl-distribution)
    - [Non-standard setups](#non-standard-setups)
        - [Activating the FRINX ODL Distribution behind a proxy](#activating-the-frinx-odl-distribution-behind-a-proxy)
        - [Activating the FRINX ODL Distribution on a server without Internet access](#activating-the-frinx-odl-distribution-on-a-server-without-internet-access)

<!-- /TOC -->
# Activating the FRINX ODL Distribution 

This guide explains how to run the distribution for the first time. If you have run it previously, please see [this guide](running-frinx-odl-after-activation.md)

***System requirements***  
**RAM:** 4GB minimum; we recommend 8GB.  
**Java:** Releases based on Beryllium and Boron (and subsequent releases) require Java 8.  
**Linux:** Unless stated otherwise, this documentation assumes you are using Linux. Supported distributions are Centos7 and Ubuntu 16.04.  
To install Java:
Ubuntu: In a terminal type

    sudo apt-get install openjdk-8-jre

CentOS: In a terminal type

    sudo yum install java-1.8.0-openjdk

## Download the FRINX ODL Distribution  

Please click on the following link to download a zip archive of the FRINX ODL Distribution:
 
*Boron*: [distribution-karaf-2.3.1.frinx.zip](https://license.frinx.io/download/distribution-karaf-2.3.1.frinx.zip)  

Older distributions can be downloaded from our [archive](https://frinx.io/archive)
By downloading the file you accept the FRINX software agreement: [EULA](7793505-v7-Frinx-ODL-Distribution-Software-End-User-License-Agreement.pdf)

## Activate your FRINX ODL Distribution  

To activate your installation, unzip the file and open the directory. Enter the following commands in a terminal to start and activate Frinx ODL (the token is unique to your user account on frinx.io and cannot be shared with other users. It can be found [here](https://frinx.io/my-licenses-information) (you need to be logged in frinx.io to view your token)

    ./bin/karaf frinx.createtoken [frinx-license_secret-token]

*Note that Frinx ODL needs approximately 3 minutes to startup and shutdown. To maintain system integrity, **please do not interrupt the startup and shutdown processes** within this time.*  
*In the event of interruption, the initial state can be restored by entering the following commands from a terminal within your Frinx ODL main directory. The first command forcibly kills the Frinx ODL karaf process; the second command cleans certain directories:*
 
```
kill -9 $(pgrep  -o -f  karaf)
rm  -rf  data/ snapshots/ journal/
```
To stop FRINX ODL safely from within the karaf console, hold the 'CTRL' key and press the 'd' key.

For more info on operating karaf, see [Operating the FRINX ODL Distribution](running-frinx-odl-after-activation)

## Non-standard setups

### Activating the FRINX ODL Distribution behind a proxy  
Please set up java system properties as described here: <https://docs.oracle.com/javase/6/docs/technotes/guides/net/proxies.html>

This means running karaf with something like this:

    JAVA_OPTS="-Dhttp.proxyHost=10.0.0.100 -Dhttp.proxyPort=8800" bin/karaf frinx.createtoken


### Activating the FRINX ODL Distribution on a server without Internet access  
Let's call the connected computer ONLINE and the one where you want to run karaf OFFLINE.

    OFFLINE# TOKEN="insert your token here"
    OFFLINE# KARAF_HOME="insert path to karaf"
    OFFLINE# echo "token=$TOKEN" &gt; $KARAF_HOME/etc/frinx.license.cfg


Generate fingerprint json to a local file:

    OFFLINE# $KARAF_HOME/bin/karaf frinx.fingerprint > fingerprint.txt


Now, copy fingerprint.txt to the ONLINE machine:

     ONLINE# curl https://license.frinx.io/api/v1/obtain-license -d "@fingerprint.txt"  -H 'Content-Type: application/json' -X PUT > frinx.license.cfg


Copy frinx.license.cfg back to OFFLINE machine, replacing the file in karaf's etc folder. You will be able to start karaf normally:

    OFFLINE# $KARAF_HOME/bin/karaf
