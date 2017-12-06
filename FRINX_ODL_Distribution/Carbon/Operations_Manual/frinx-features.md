[Documentation main page](https://frinxio.github.io/Frinx-docs/)
[Operations Manual main page](https://frinxio.github.io/Frinx-docs/FRINX_ODL_Distribution/Carbon/operations_manual.html)
<!-- TOC -->

- [FRINX ODL Distribution: Features](#frinx-odl-distribution-features)
    - [Managing features](#managing-features)

<!-- /TOC -->

# FRINX ODL Distribution: Features

The FRINX distribution offers the following features:

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

## Managing features
Feature management has changed after FRINX has introduced DAEXIM (Data Export and Import Manager) in Carbon. Previously, features that the user loaded in Karaf were persisted after shutdown of Frinx ODL. Since introducing DAEXIM, features loaded in karaf are no longer persisted automatically.
 
For features that the user wants to always start every time FRINX ODL is started, features must be added to the following file located in the Frinx ODL etc/ folder:

**org.apache.karaf.features.cfg**

Edit the file to remove the '#' in front of **odlFeaturesBoot** and specify which features to persistently load every time Frinx ODL starts e.g. to ensure that the odl-restconf and odl-netconf-connector-all features are always present at startup you would edit odlFeaturesBoot to read as follows:

odlFeaturesBoot=odl-restconf,odl-netconf-connector-all

For a list of available features, first start Frinx ODL [see our guide](running-frinx-odl-after-activation) and enter the following command within the karaf terminal:

    feature:list

For a list of features whose names include a particular word e.g. 'restconf' you can use grep e.g.

    feature:list |grep restconf

