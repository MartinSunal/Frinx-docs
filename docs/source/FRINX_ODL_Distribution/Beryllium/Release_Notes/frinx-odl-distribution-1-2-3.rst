
frinx-odl-base-feature-content-rel-1-2-3
========================================

This document describes the latest changes, additions, known issues, and fixes for the Frinx ODL Distribution. In the first section, issues found during testing the Frinx controller are described together with their workarounds. The causes of both issues are present in  the upstream Opendaylight Beryllium SR2. In the second section we provide links to the relevant Opendaylight Beryllium SR2 release notes. 

Additions
---------

Licensing capabilities implemented Implementation of licensing capabilities in Frinx controller 1.2.3
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Known Issues
------------

Karaf clean fails to remove all configuration changes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

After resetting the frinx controller by running karaf clean, you may see in the GUI that no features are loaded. The netconf XML used to configure ODL is in a file called etc/opendaylight/current/controller.currentconfig.xml. But karaf clean starts only features mentioned in etc/org.apache.karaf.features.cfg (featuresBoot), the required features (odl-netconf-client and more) are not found and thus startup fails. 

**Typical Symptoms:** No features shown in GUI Log contains  *"Giving up (after 100 retries) on Karaf featuresService.listInstalledFeatures()"* **Workaround:** Clean install of frinx controller *>karaf clean* and removing contents of etc/opendaylight/current/controller.currentconfig.xml 

Config subsystem times out while loading feature
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

After installing SNMP feature then making an RESTCONF request via SNMP request to a node with a running SNMP client you will recieve a error HTTP 501 ERROR with the response body containing "No implementation of RPC AbsoluteSchemaPath{path=[(urn:opendaylight:snmp?revision=2014-09-22)snmp-get]} available" This is a error in the upstream opendaylight project and tracked by Bug 6018 

**Typical Symptoms:** Unable to make SNMP-get request because "No implementation of RPC available" Log contains  *"Caused by: org.opendaylight.controller.config.persist.impl.ConfigPusherImpl$NotEnoughCapabilitiesException:"* **Workaround:** Restart the frinx controller. Use */bin/karaf* or */bin/start*\ , Do not use *>karaf clean* 

Opendaylight Beryllium SR2 Release Notes The Frinx controller 1.2.3 is based on Opendaylight Beryllium SR2. Where a feature is present in both controllers, the same Release Notes apply
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes odlparent https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#ODL_Root_Parent yangtools https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#YANG_Tools mdsal https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#MD-SAL controller (without xsql) https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#Controller netconf https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#NETCONF aaa `https://wiki.opendaylight.org/view/Simultaneous*Release/Beryllium/SR2/Release_Notes#Authentication.2C_Authorization_and_Accounting*.28AAA.29 <https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#Authentication.2C_Authorization_and_Accounting_.28AAA.29>`__ dlux topoprocessing https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#Topology_Processing_Framework snmp https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#SNMP_Plugin openflowjava and openflowplugin https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#OpenFlow_Plugin neutron `https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#Neutron_Northbound <https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#OpenFlow_Plugin>`__ sfc https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#Service_Function_Chaining ovsdb _(without netvirt) https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#OVSDB_Integration gbp `https://wiki.opendaylight.org/view/Simultaneous*Release/Beryllium/SR2/Release_Notes#Group_Based_Policy*.28 <https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#Group_Based_Policy_.28>`__ l2switch https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#L2_Switch bgpcep https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#BGP_PCEP lispflowmapping https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#LISP_Flow_Mapping [/wpmem_form]
