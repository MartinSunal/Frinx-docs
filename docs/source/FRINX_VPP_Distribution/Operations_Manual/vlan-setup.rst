
`Documentation main page <https://frinxio.github.io/Frinx-docs/>`_
`VPP Operations Manual main page <https://frinxio.github.io/Frinx-docs/FRINX_VPP_Distribution/operations_manual.html>`_

VLAN Setup
==========


*  `Topology <#topology>`__
*  `Config <#config>`__
*  `Classifier API (ACLs) <#classifier-api-acls>`__

Topology
--------

.. code-block:: guess

   +-------------------------+                    +-------------------------+
   |host1                    |                    |                    host2|
   |                         |                    |                         |
   |    +------------+       |                    |       +------------+    |
   |    |VPP1        |     +-+      VLAN 10       +-+     |        VPP2|    |
   |    |      +     +-----+ +--------------------+ +-----+     +      |    |
   |    |     loop0  |     +-+                    +-+     |    loop0   |    |
   |    +------------+       |                    |       +------------+    |
   |                         |                    |                         |
   |                         |                    |                         |  
   +-------------------------+                    +-------------------------+  



Config
------

.. code-block:: guess

   ### host1
   sudo ip link set enp0s8 down
   sudo service vpp restart

   ### VPP1
   create loopback interface mac de:ad:00:00:00:01
   set int ip address loop0 192.168.10.1/24
   set int l2 bridge loop0 1
   set int state loop0 up
   set inter state GigabitEthernet0/8/0 up
   create sub GigabitEthernet0/8/0 10
   set interface state GigabitEthernet0/8/0.10 up
   set interface l2 bridge GigabitEthernet0/8/0.10 1
   set inter l2 tag-rewrite GigabitEthernet0/8/0.10 pop 1
   set ip arp GigabitEthernet0/8/0.10 192.168.10.2 de:ad:00:00:00:02
   trace add dpdk-input 50

   ### host2
   sudo ip link set enp0s8 down
   sudo service vpp restart

   ### VPP2
   create loopback interface mac de:ad:00:00:00:02
   set int ip address loop0 192.168.10.2/24
   set int l2 bridge loop0 1
   set int state loop0 up
   set inter state GigabitEthernet0/8/0 up
   create sub GigabitEthernet0/8/0 10
   set interface state GigabitEthernet0/8/0.10 up
   set interface l2 bridge GigabitEthernet0/8/0.10 1
   set inter l2 tag-rewrite GigabitEthernet0/8/0.10 pop 1
   set ip arp GigabitEthernet0/8/0.10 192.168.10.1 de:ad:00:00:00:01
   ping 192.168.10.1 source loop0 repeat 2



Classifier API (ACLs)
---------------------

To disable ICMP traffic on an interface invoke following commands in VAT:

.. code-block:: guess

   classify_add_del_table mask l3 ip4 proto
   classify_add_del_session acl-hit-next deny table-index 0 skip_n 1 match_n 1 match l3 ip4 proto 1
   # For l2 interface
   classify_set_interface_l2_tables sw_if_index 1 ip4-table 0
   # For l3 interface
   classify_set_interface_ip_table sw_if_index 1 table 0



Similarly to disabling traffic from a certain IP, replace the table and session creation with:

.. code-block:: guess

   classify_add_del_table mask l3 ip4 src
   classify_add_del_session acl-hit-next deny table-index 0 skip_n 1 match_n 1 match l3 ip4 src 192.168.1.10
