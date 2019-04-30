
RESTCONF
========

*  `Flow operations using RESTCONF <#flow-operations-using-restconf>`__
*  `Examining OpenFlow devices using RESTCONF <#examining-openflow-devices-using-restconf>`__
*  `Next steps <#next-steps>`__

Flow operations using RESTCONF
------------------------------

To use RESTCONF, a RESTful API client is needed.

The Frinx ODL Distribution comes with one preinstalled: the **YangUI**.

An alternative is to use the **Chrome Postman** extension: `https://www.getpostman.com <https://www.getpostman.com/>`__

Many OpenDaylight projects using RESTCONF have made examples of HTTP requests available as `Postman libraries <https://wiki.opendaylight.org/view/OpenDaylight_OpenFlow_Plugin::End_to_End_Inventory#How_to_hit_RestConf_with_Postman>`__

Examining OpenFlow devices using RESTCONF
-----------------------------------------

The operational datastore containing the OpenFlow nodes is exposed at the URL below. **\ *Warning: Datastores contain a large amount of data and loading them into a web browser may cause a temporary lockup.**

.. code-block:: guess

   HTTP Method: GET  
   HTTP URL: <http://localhost:8181/restconf/operational/opendaylight-inventory:nodes>#  
   HTTP Headers: Content-type: application/xml Accept: application/xml


To see the flows on node openflow:1 only

.. code-block:: guess

   HTTP Method: GET  
   HTTP URL: [http://10.10.199.149:8181/restconf/operational/opendaylight-inventory:nodes/node/openflow:1 ][3]  
   HTTP Headers: Content-type: application/xml Accept: application/xml


To program the openflow:1 switch, send an HTTP POST request to the controller. The payload and the URI are defined in the YANG model used.

.. code-block:: guess

   HTTP Method: POST  
   HTTP URL: [http://localhost:8181/restconf/config/opendaylight-inventory:nodes/node/openflow:1/table/0/flow/1 ][4]  
   HTTP Headers: Content-type: application/xml Accept: application/xml

   HTTP Content: <img class="aligncenter wp-image-2472 size-full" src="https://frinx.io/wp-content/uploads/2016/06/restconf-http.png" alt="restconf-http" width="787" height="695" />


We expect any switch which implements OpenFlow versions 1.0 and 1.3.2. to be programmable by the Frinx ODL Distribution. A list of such switches is maintained `here <https://www.opennetworking.org/sdn-openflow-products?limitstart=0>`__ Note that if you already have one of these devices, you may need to upgrade its firmware.

Next steps
----------

The OpenDaylight `user guide <https://nexus.opendaylight.org/content/sites/site/org.opendaylight.docs/master/userguide/manuals/userguide/bk-user-guide/content/_l2switch.html>`__ describes more operations which are possible using the l2switch feature.
