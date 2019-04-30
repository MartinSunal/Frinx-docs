
frinx-odl-base-feature-content-rel-1-2-5
========================================

This document describes the latest changes, additions, known issues, and fixes for the Frinx ODL Distribution. In the first section, issues found during testing of the Frinx controller are described, together with their workarounds. The causes of both issues are present in  the upstream Opendaylight Beryllium SR2. In the second section we provide links to the relevant Opendaylight Beryllium SR2 release notes.  

Additions Minor bug fixes Removed hawt.io Extended licensing logging
--------------------------------------------------------------------

Known Issues
------------

Karaf clean fails to remove all configuration changes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

After resetting the frinx controller by running karaf clean, you may see in the GUI that no features are loaded. The netconf XML used to configure ODL is in a file called etc/opendaylight/current/controller.currentconfig.xml. But karaf clean starts only features mentioned in etc/org.apache.karaf.features.cfg (featuresBoot), the required features (odl-netconf-client and more) are not found and thus startup fails. 

**Typical Symptoms:** No features shown in GUI Log contains  *"Giving up (after 100 retries) on Karaf featuresService.listInstalledFeatures()"* **Workaround:** Clean install of frinx controller *>karaf clean* and removing contents of etc/opendaylight/current/controller.currentconfig.xml 

A feature fails to operate correctly after it is installed
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Timeouts for the Opendaylight config-persister-feature-adapter are too shortlong. Pushing of feature xmls is retried only for 100ms, which is sometimes not long enough during clean karaf startup. In this case yang models for a feature may not be available. This is an error in the upstream Opendaylight project and tracked by Bug 6018 

**Typical Symptoms:** The symptoms vary but this happened several times during system testing. The cause is identified by searching the log for "Giving up (after 100 retries)." If the SNMP feature failed to load, then after installing the SNMP feature and then making a RESTCONF request via an SNMP request to a node with a running SNMP client you will recieve an HTTP 501 ERROR with the response body containing “No implementation of RPC AbsoluteSchemaPath{path=[(urn:opendaylight:snmp?revision=2014-09-22)snmp-get]} available” The og contains  **“Caused by: org.opendaylight.controller.config.persist.impl.ConfigPusherImpl$NotEnoughCapabilitiesException:”** **Workaround:** The workaround is to restart karaf without cleaning the data directory so that the cache is available. Use **/bin/karaf** or **/bin/start**, Do not use**>karaf clean** 

Opendaylight Beryllium SR2 Release Notes The Frinx controller 1.2.3 is based on Opendaylight Beryllium SR2. Where a feature is present in both controllers, the same Release Notes apply
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes odlparent https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#ODL_Root_Parent yangtools https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#YANG_Tools mdsal https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#MD-SAL controller (without xsql) https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#Controller netconf https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#NETCONF aaa `https://wiki.opendaylight.org/view/Simultaneous*Release/Beryllium/SR2/Release_Notes#Authentication.2C_Authorization_and_Accounting*.28AAA.29 <https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#Authentication.2C_Authorization_and_Accounting_.28AAA.29>`__ dlux topoprocessing https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#Topology_Processing_Framework snmp https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#SNMP_Plugin openflowjava and openflowplugin https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#OpenFlow_Plugin neutron `https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#Neutron_Northbound <https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#OpenFlow_Plugin>`__ sfc https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#Service_Function_Chaining ovsdb (without netvirt) https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#OVSDB_Integration gbp `https://wiki.opendaylight.org/view/Simultaneous*Release/Beryllium/SR2/Release_Notes#Group_Based_Policy*.28 <https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#Group_Based_Policy_.28>`__ l2switch https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#L2_Switch bgpcep https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#BGP_PCEP lispflowmapping https://wiki.opendaylight.org/view/Simultaneous_Release/Beryllium/SR2/Release_Notes#LISP_Flow_Mapping [/wpmem_form]
