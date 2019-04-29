
`Documentation main page <https://frinxio.github.io/Frinx-docs/>`_
`VPP Operations Manual main page <https://frinxio.github.io/Frinx-docs/FRINX_VPP_Distribution/operations_manual.html>`_

Installation Tips
=================

*  `VPP Installation <#vpp-installation>`__
*  `Install VPP in a docker container <#install-vpp-in-a-docker-container>`__
*  `Installing binary packages <#installing-binary-packages>`__

VPP Installation
----------------

Described at: `VPP install packages <https://wiki.fd.io/view/VPP/Installing_VPP_binaries_from_packages>`_

If VPP does not pick up interfaces, it helps to build and install it manually:

.. code-block:: guess

   ./build-root/vagrant/build.sh
   cd build-root
   sudo dpkg -i *.deb
   sudo service vpp start



**The following resources are useful in figuring out how to do (configure) something with VPP**


#. https://wiki.fd.io/view/VPP
#. `https://docs.fd.io/vpp/17.04/ <https://wiki.fd.io/view/VPP>`_
#. Google
#. *CSIT (VPP integration tests) - they use VAT tool to provide complex configuration to VPP. Each test case logs exact configuration steps. So looking at log file from the latest vpp functional test run can help https://jenkins.fd.io/view/csit/job/csit-vpp-functional-master-ubuntu1604-virl/ If the log file does not open directly, download and reopen in browser). Look at a test case, list of keywords and VAT command executed in every step.*
#. vpp-dev@lists.fd.io

Install VPP in a docker container
---------------------------------

After docker is installed start a centos7 container:

.. code-block:: guess

   sudo docker run -ti --privileged centos7 /bin/bash


Then in the container install vpp, configure and start it:

.. code-block:: guess

    yum install install apt-transport-https vim net-tools inetutils-ping telnet

       cat <<EOT >> /etc/yum.repos.d/fdio-release.repo
       [fdio-release]
       name=fd.io release branch latest merge
       baseurl=https://nexus.fd.io/content/repositories/fd.io.centos7/
       enabled=1
       gpgcheck=0
       EOT

       yum install vpp



More info on VPP install: https://wiki.fd.io/view/VPP/Installing_VPP_binaries_from_packages

.. code-block:: guess

   vim /etc/vpp/startup.conf


Change DPDK configuration to add "no-pci" config attribute

.. code-block:: guess

   dpdk {
       uio-driver uio_pci_generic
       no-pci
   }


And enable cli

.. code-block:: guess

   unix {
   nodaemon
   log /tmp/vpp.log
   full-coredump
   cli-listen localhost:5002
   }

   /usr/bin/vpp -c /etc/vpp/startup.conf &
   telnet 0 5002



To save the modified container invoke from host:

.. code-block:: guess

   sudo docker commit a4fdd4da4500 temp/vppcentos



Installing binary packages
--------------------------

Instructions for consuming publicly available binary packages of FD.FRINX.io distribution

**Centos7**

In file:

.. code-block:: guess

   /etc/yum.repos.d/frinx-fdio-release.repo



Set content:

.. code-block:: guess

   [frinx-fdio-release]
   name=FRINX fd.io release branch latest merge
   baseurl=https://<CustomerID>:<CustomerPassword>@nexus.fd.frinx.io/nexus/content/repositories/fd.io.centos7/
   enabled=1
   gpgcheck=0
   sslverify=0



**Make sure to change the CustomerID and password in the repository settings**

Installation with YUM can be done with:

.. code-block:: guess

   sudo yum install vpp vpp-plugins vpp-monitoring-agent

