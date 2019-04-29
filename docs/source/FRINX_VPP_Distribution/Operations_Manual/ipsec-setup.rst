
`Documentation main page <https://frinxio.github.io/Frinx-docs/>`_
`VPP Operations Manual main page <https://frinxio.github.io/Frinx-docs/FRINX_VPP_Distribution/operations_manual.html>`_

IPsec Setup
===========

Table of content

*  `VPP1 hub <#vpp1-hub>`__
*  `VPP2 (spoke1) <#vpp2-spoke1>`__
*  `VPP3 (spoke2) <#vpp3-spoke2>`__


Configure VPP from the console using the following commands:

VPP1 (hub)
----------

.. code-block:: guess

   create loopback interface
   set int ip address loop0 192.168.10.1/24
   set int state loop0 up
   set int ip address GigabitEthernet0/8/0 192.168.56.10/24
   set int state GigabitEthernet0/8/0 up
   set ip arp GigabitEthernet0/8/0 192.168.56.20 08:00:27:49:1f:56
   set ip arp GigabitEthernet0/8/0 192.168.56.30 08:00:27:e7:aa:74
   ip route add 192.168.20.0/24 via 192.168.56.20 GigabitEthernet0/8/0
   ip route add 192.168.30.0/24 via 192.168.56.30 GigabitEthernet0/8/0
   # hub O====O spoke1
   ipsec sa add 10 spi 1020 esp crypto-key 56a10cccccccccccccccccccccc56a20 crypto-alg aes-cbc-128 integ-key 56a10cccccccccccccccccccccccccccccc56a20 integ-alg sha1-96 tunnel-src 192.168.56.10 tunnel-dst 192.168.56.20
   ipsec sa add 20 spi 2010 esp crypto-key 56a20cccccccccccccccccccccc56a10 crypto-alg aes-cbc-128 integ-key 56a20cccccccccccccccccccccccccccccc56a10 integ-alg sha1-96 tunnel-src 192.168.56.20 tunnel-dst 192.168.56.10
   # hub O====O spoke2
   ipsec sa add 30 spi 1030 esp crypto-key 56a10cccccccccccccccccccccc56a30 crypto-alg aes-cbc-128 integ-key 56a10cccccccccccccccccccccccccccccc56a30 integ-alg sha1-96 tunnel-src 192.168.56.10 tunnel-dst 192.168.56.30
   ipsec sa add 40 spi 3010 esp crypto-key 56a30cccccccccccccccccccccc56a10 crypto-alg aes-cbc-128 integ-key 56a30cccccccccccccccccccccccccccccc56a10 integ-alg sha1-96 tunnel-src 192.168.56.30 tunnel-dst 192.168.56.10
   ipsec spd add 1
   set interface ipsec spd GigabitEthernet0/8/0 1
   ipsec policy add spd 1 priority 100 inbound action bypass protocol 50
   ipsec policy add spd 1 priority 100 outbound action bypass protocol 50
   # MATCH traffic between hub:loop0 and spoke1:loop0 => send it to tunnel hub O====O spoke1
   ipsec policy add spd 1 outbound  priority 10 action protect sa 10 local-ip-range 192.168.10.1 - 192.168.10.1 remote-ip-range 192.168.20.1 - 192.168.20.1
   ipsec policy add spd 1 inbound priority 10 action protect sa 20 local-ip-range 192.168.10.1 - 192.168.10.1 remote-ip-range 192.168.20.1 - 192.168.20.1
   # MATCH traffic between hub:loop0 and spoke2:loop0 => send it to tunnel hub O====O spoke2
   ipsec policy add spd 1 outbound  priority 10 action protect sa 30 local-ip-range 192.168.10.1 - 192.168.10.1 remote-ip-range 192.168.30.1 - 192.168.30.1
   ipsec policy add spd 1 inbound priority 10 action protect sa 40 local-ip-range 192.168.10.1 - 192.168.10.1 remote-ip-range 192.168.30.1 - 192.168.30.1
   # MATCH traffic between spoke2:loop0 and spoke1:loop0 => send it to tunnel hub O====O spoke1
   ipsec policy add spd 1 outbound  priority 10 action protect sa 10 local-ip-range 192.168.30.1 - 192.168.30.1 remote-ip-range 192.168.20.1 - 192.168.20.1
   ipsec policy add spd 1 inbound priority 10 action protect sa 20 local-ip-range 192.168.30.1 - 192.168.30.1 remote-ip-range 192.168.20.1 - 192.168.20.1
   # MATCH traffic between spoke1:loop0 and spoke2:loop0 => send it to tunnel hub O====O spoke2
   ipsec policy add spd 1 outbound  priority 10 action protect sa 30 local-ip-range 192.168.20.1 - 192.168.20.1 remote-ip-range 192.168.30.1 - 192.168.30.1
   ipsec policy add spd 1 inbound priority 10 action protect sa 40 local-ip-range 192.168.20.1 - 192.168.20.1 remote-ip-range 192.168.30.1 - 192.168.30.1

   trace add dpdk-input 50



VPP2 (spoke1)
-------------

.. code-block:: guess

   create loopback interface
   set int ip address loop0 192.168.20.1/24
   set int state loop0 up
   set int ip address GigabitEthernet0/8/0 192.168.56.20/24
   set int state GigabitEthernet0/8/0 up
   set ip arp GigabitEthernet0/8/0 192.168.56.10 08:00:27:65:2e:36
   ip route add 192.168.10.0/24 via 192.168.56.10 GigabitEthernet0/8/0
   ip route add 192.168.30.0/24 via 192.168.56.10 GigabitEthernet0/8/0
   # hub O====O spoke1
   ipsec sa add 10 spi 2010 esp crypto-key 56a20cccccccccccccccccccccc56a10 crypto-alg aes-cbc-128 integ-key 56a20cccccccccccccccccccccccccccccc56a10 integ-alg sha1-96 tunnel-src 192.168.56.20 tunnel-dst 192.168.56.10
   ipsec sa add 20 spi 1020 esp crypto-key 56a10cccccccccccccccccccccc56a20 crypto-alg aes-cbc-128 integ-key 56a10cccccccccccccccccccccccccccccc56a20 integ-alg sha1-96 tunnel-src 192.168.56.10 tunnel-dst 192.168.56.20
   ipsec spd add 1
   set interface ipsec spd GigabitEthernet0/8/0 1
   ipsec policy add spd 1 priority 100 inbound action bypass protocol 50
   ipsec policy add spd 1 priority 100 outbound action bypass protocol 50
   # MATCH traffic between spoke1:loop0 and hub:loop0 => send it to tunnel
   ipsec policy add spd 1 outbound  priority 10 action protect sa 10 local-ip-range 192.168.20.1 - 192.168.20.1 remote-ip-range 192.168.10.1 - 192.168.10.1
   ipsec policy add spd 1 inbound priority 10 action protect sa 20 local-ip-range 192.168.20.1 - 192.168.20.1 remote-ip-range 192.168.10.1 - 192.168.10.1
   # MATCH traffic between spoke1:loop0 and spoke2:loop0 => send it to tunnel
   ipsec policy add spd 1 outbound  priority 10 action protect sa 10 local-ip-range 192.168.20.1 - 192.168.20.1 remote-ip-range 192.168.30.1 - 192.168.30.1
   ipsec policy add spd 1 inbound priority 10 action protect sa 20 local-ip-range 192.168.20.1 - 192.168.20.1 remote-ip-range 192.168.30.1 - 192.168.30.1

   ping 192.168.10.1 source loop0 repeat 2



VPP3 (spoke2)
-------------

.. code-block:: guess

   create loopback interface
   set int ip address loop0 192.168.30.1/24
   set int state loop0 up
   set int ip address GigabitEthernet0/8/0 192.168.56.30/24
   set int state GigabitEthernet0/8/0 up
   set ip arp GigabitEthernet0/8/0 192.168.56.10 08:00:27:65:2e:36
   ip route add 192.168.10.0/24 via 192.168.56.10 GigabitEthernet0/8/0
   ip route add 192.168.20.0/24 via 192.168.56.10 GigabitEthernet0/8/0
   # hub O====O spoke2
   ipsec sa add 10 spi 3010 esp crypto-key 56a30cccccccccccccccccccccc56a10 crypto-alg aes-cbc-128 integ-key 56a30cccccccccccccccccccccccccccccc56a10 integ-alg sha1-96 tunnel-src 192.168.56.30 tunnel-dst 192.168.56.10
   ipsec sa add 20 spi 1030 esp crypto-key 56a10cccccccccccccccccccccc56a30 crypto-alg aes-cbc-128 integ-key 56a10cccccccccccccccccccccccccccccc56a30 integ-alg sha1-96 tunnel-src 192.168.56.10 tunnel-dst 192.168.56.30
   ipsec spd add 1
   set interface ipsec spd GigabitEthernet0/8/0 1
   ipsec policy add spd 1 priority 100 inbound action bypass protocol 50
   ipsec policy add spd 1 priority 100 outbound action bypass protocol 50
   # MATCH traffic between spoke2:loop0 and hub:loop0 => send it to tunnel
   ipsec policy add spd 1 outbound  priority 10 action protect sa 10 local-ip-range 192.168.30.1 - 192.168.30.1 remote-ip-range 192.168.10.1 - 192.168.10.1
   ipsec policy add spd 1 inbound priority 10 action protect sa 20 local-ip-range 192.168.30.1 - 192.168.30.1 remote-ip-range 192.168.10.1 - 192.168.10.1
   # MATCH traffic between spoke2:loop0 and spoke1:loop0 => send it to tunnel
   ipsec policy add spd 1 outbound  priority 10 action protect sa 10 local-ip-range 192.168.30.1 - 192.168.30.1 remote-ip-range 192.168.20.1 - 192.168.20.1
   ipsec policy add spd 1 inbound priority 10 action protect sa 20 local-ip-range 192.168.30.1 - 192.168.30.1 remote-ip-range 192.168.20.1 - 192.168.20.1

   ping 192.168.10.1 source loop0 repeat 2

   ping 192.168.20.1 source loop0 repeat 2
