.. role:: raw-html-m2r(raw)
   :format: html


`Documentation main page <https://frinxio.github.io/Frinx-docs/>`_
`VPP operations manual <https://frinxio.github.io/Frinx-docs/FRINX_VPP_Distribution/operations_manual.html>`_  

Introduction
============

FRINX provides an FD.io distribution.\ :raw-html-m2r:`<br>`
FD.io is an opensource project that among other things provides the Vector Packet Processor. More information can be found at `FD.io <http://fd.io>`_ :raw-html-m2r:`<br>`
This page contains the details about the FRINX fd.io distribution.


Table of contents:

* `Features <#features>`__
* `Operations <#operations>`__
* `Jenkins jobs <#jenkins-jobs>`__

Features
--------

**Project imported from open source:**


*     VPP

  * No changes

**Internal projects:**


*     VPP-monitoring-agent (magent)

Operations
----------

**CI/CD**

There is a custom CI/CD pipeline for the FRINX fd.io distribution. It is based on `SBE <../../FRINX_Smart_Build_Engine/Introduction/sbe_intro.md>`_.\ :raw-html-m2r:`<br>`
The following diagram shows the relationships between FD.io and FD.FRINX.io:


.. image:: fdio.png
   :target: fdio.png
   :alt: fdio


Jenkins jobs
------------

The following diagrams list the jenkins jobs imported from opensource FD.io (green marks imported):


.. image:: vpp.png
   :target: vpp.png
   :alt: vpp

