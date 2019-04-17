.. role:: raw-html-m2r(raw)
   :format: html


`Documentation main page <https://frinxio.github.io/Frinx-docs/>`_
`Operations Manual main page <https://frinxio.github.io/Frinx-docs/FRINX_ODL_Distribution/Carbon/operations_manual.html>`_

FRINX ODL Distribution: Features
================================


.. raw:: html

   <!-- TOC -->




* `FRINX ODL Distribution: Features <#frinx-odl-distribution-features>`_

  * `Managing features <#managing-features>`_

:raw-html-m2r:`<!-- /TOC -->`
The FRINX distribution offers the following features:

.. code-block::

    odlparent
    yangtools
    mdsal
    controller (without xsql)
    netconf
    aaa
    dlux
    topoprocessing
    snmp
    openflowjava
    openflowplugin
    neutron
    sfc
    ovsdb
    gbp
    l2switch
    bgpcep
    lispflowmapping
    cli
    daexim
    faas
    genius
    netvirt
    honeycomb-vbd
    l2vpn
    l3vpn
    uniconfig


Managing features
-----------------

Feature management has changed after FRINX has introduced DAEXIM (Data Export and Import Manager) in Carbon. Previously, features that the user loaded in Karaf were persisted after shutdown of FRINX ODL. Since introducing DAEXIM, features loaded in karaf are no longer persisted automatically.

For features that the user wants to always start every time FRINX ODL is started, features must be added to the following file located in the FRINX ODL etc/ folder:

**org.apache.karaf.features.cfg**

Edit the file to remove the '#' in front of **odlFeaturesBoot** and specify which features to persistently load every time FRINX ODL starts e.g. to ensure that the odl-restconf and odl-netconf-connector-all features are always present at startup you would edit odlFeaturesBoot to read as follows:

odlFeaturesBoot=odl-restconf,odl-netconf-connector-all

For a list of available features, first start FRINX ODL (\ `see our guide <running-frinx-odl-after-activation>`_\ ) and enter the following command within the karaf terminal:

.. code-block::

   feature:list


A list of installed features can be displayed with the command:

.. code-block::

   feature:list -i


For a list of features whose names include a particular word e.g. 'restconf' you can use grep e.g.

.. code-block::

   feature:list | grep restconf
