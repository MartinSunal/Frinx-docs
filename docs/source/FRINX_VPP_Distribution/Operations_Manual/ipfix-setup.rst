
`Documentation main page <https://frinxio.github.io/Frinx-docs/>`_
`VPP Operations Manual main page <https://frinxio.github.io/Frinx-docs/FRINX_VPP_Distribution/operations_manual.html>`_

IPFIX Setup
===========

Table of content

*  `Configure VPP using VAT <#configure-vpp-using-vat>`__
*  `Start a UDP server <#start-a-udp-server>`__
*  `Further configuration options <#further-configuration-options>`__
*  `IPFIX limitations as of 17.01 <#ipfix-limitations-as-of-17-01>`__

Configure VPP using VAT
-----------------------

**Set interface on index 1: up + IP**

.. code-block:: guess

   sw_interface_set_flags sw_if_index 1 admin-up

   sw_interface_add_del_address sw_if_index 1 192.168.1.87/24



**Configure IPFIX export from interface 1's IP address to collector at 192.168.1.101 port 4739**

.. code-block:: guess

   ip_neighbor_add_del sw_if_index 1 dst 192.168.1.101 mac 08:00:27:82:c1:90

   classify_add_del_table mask l3 ip4 src

   classify_add_del_session acl-hit-next permit table-index 0 skip_n 1 match_n 1 match l3 ip4 src 192.168.1.101

   flow_classify_set_interface sw_if_index 1 ip4-table 0

   set_ipfix_exporter collector_address 192.168.1.101 src_address 192.168.1.87 template_interval 5

   set_ipfix_classify_stream domain 1

   ipfix_classify_table_add_del table 0 ip4 add



... this configures VPP's interface with index 1 to export IPFIX statistics and send them to 192.168.1.101:4739 UDP server


Start a UDP server
^^^^^^^^^^^^^^^^^^

.. code-block:: guess

   nc -ul 4739



... this bash UDP server will accept the IPFIX data and display on screen. A better way of reading the data is using Wireshark (you can ping 192.168.1.87 to increase the stats) **Sample capture from Wireshark** <img src="https://frinx.io/wp-content/uploads/2017/05/ipfix.png" alt="" width="1252" height="800" class="alignleft size-full wp-image-4588" />


Further configuration options
-----------------------------

To export IP source, IP destination, Protocol configure classifier with:

.. code-block:: guess

   classify_add_del_table mask l3 ip4 src dst proto l4

   flow_classify_set_interface sw_if_index 1 ip4-table 0



To also export ports, configure with:

.. code-block:: guess

   classify_add_del_table mask l3 ip4 src dst proto l4 src_port dst_port



IPFIX limitations as of 17.01
-----------------------------

IPFIX has some limitations that (might) limit its usage within real use cases:


#. Only inbound/ingress traffic is matched/exported by IPFIX
#. When using both IPFIX and IPSEC, the traffic is always going through IPFIX node before IPSEC decrypt, making IPFIX not work at all - there is an issue with the node graph order
#. Matching L4 ports is also triggered for port-less protocols like ICMP, exporting each ICMP packet as new flow (since ports are assigned random numbers), which makes IPFIX export packets too big
#. Each flow/connection creates a new classify session in order to be able to report it via IPFIX, however the sessions are not "garbage collected" making this a memory leak
#. Due to creating new sessions for each flow, it is impossible to customize IPFIX matching e.g. match only TCP/UDP protocols, any IP with a port range
