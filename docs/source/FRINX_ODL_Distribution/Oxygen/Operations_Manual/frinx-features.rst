
FRINX ODL Distribution Features
===============================

* `Managing features <#managing-features>`__

The FRINX distribution offers the following features:

.. code-block:: guess

    odlparent
    yangtools
    mdsal
    controller (without xsql)
    netconf
    aaa
    gbp
    cli
    daexim
    uniconfig
    unitopo


Managing features
-----------------

Feature management has changed after FRINX has introduced DAEXIM (Data Export and Import Manager) in Oxygen. Previously, features that the user loaded in Karaf were persisted after shutdown of FRINX ODL. Since introducing DAEXIM, features loaded in karaf are no longer persisted automatically.

For features that the user wants to always start every time FRINX ODL is started, features must be added to the following file located in the FRINX ODL etc/ folder:

**org.apache.karaf.features.cfg**

Edit the file to remove the '#' in front of **odlFeaturesBoot** and specify which features to persistently load every time FRINX ODL starts e.g. to ensure that the odl-restconf and odl-netconf-connector-all features are always present at startup you would edit odlFeaturesBoot to read as follows:

odlFeaturesBoot=odl-restconf,odl-netconf-connector-all

For a list of available features, first start FRINX ODL see our guide (:doc:`running-frinx-odl-after-activation`) and enter the following command within the karaf terminal:

.. code-block:: guess

   feature:list


A list of installed features can be displayed with the command:

.. code-block:: guess

   feature:list -i


For a list of features whose names include a particular word e.g. 'restconf' you can use grep e.g.

.. code-block:: guess

   feature:list | grep restconf
