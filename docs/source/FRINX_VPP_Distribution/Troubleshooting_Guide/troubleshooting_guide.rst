
`Documentation main page <https://frinxio.github.io/Frinx-docs/>`_
`VPP operations manual <https://frinxio.github.io/Frinx-docs/FRINX_VPP_Distribution/operations_manual.html>`_  

Troubleshooting Guide
=====================

This guide shows how to run VPP in debug mode. It enables you to:


* Collect trace if VPP crashes 
* Debug VPP with break-points 

**Set VPP config:**

.. code-block:: guess

   unix {
   nodaemon
   interactive
   log /tmp/vpp.log



**Build VPP with symbols:**

.. code-block:: guess

   [root@vpp2 build-root]# make PLATFORM=vpp TAG=vpp_debug vpp-install



**Run with gdb:**

.. code-block:: guess

   [root@vpp2 build-root]# gdb ./install-vpp_debug-native/vpp/bin/vpp



**Run VPP:**

.. code-block:: guess

   (gdb) run -c /etc/vpp/startup.conf



**if vpp crashes then type ctrl-c to get to the gdb prompt then backtrace as follows:**

.. code-block:: guess

   DBGvpp#
   Program received signal SIGINT, Interrupt.
   0x00007ffff51e1977 in __GI_epoll_pwait (epfd=9, events=0x7fffb5f8b364, maxevents=256, timeout=0, set=0x7ffff799e360 <unblock_all_signals.14527>)
       at ../sysdeps/unix/sysv/linux/epoll_pwait.c:48
   48             int result = INLINE_SYSCALL (epoll_pwait, 6, epfd, events, maxevents,
   Missing separate debuginfos, use: debuginfo-install openssl-libs-1.0.1e-60.el7_3.1.x86_64
   (gdb) bt
   #0  0x00007ffff51e1977 in __GI_epoll_pwait (epfd=9, events=0x7fffb5f8b364, maxevents=256, timeout=0, set=0x7ffff799e360 <unblock_all_signals.14527>)
       at ../sysdeps/unix/sysv/linux/epoll_pwait.c:48
   #1  0x00007ffff7780ef9 in linux_epoll_input (vm=0x7ffff77668c0 <vlib_global_main>, node=0x7fffb4fcec00, frame=0x0)
       at /home/localadmin/vpp.17.01.3/build-data/../vlib/vlib/unix/input.c:153
   #2  0x00007ffff74efd8c in dispatch_node (vm=0x7ffff77668c0 <vlib_global_main>, node=0x7fffb4fcec00, type=VLIB_NODE_TYPE_PRE_INPUT,
       dispatch_state=VLIB_NODE_STATE_POLLING, frame=0x0, last_time_stamp=163901337266360) at /home/localadmin/vpp.17.01.3/build-data/../vlib/vlib/main.c:998
   #3  0x00007ffff74f1748 in vlib_main_loop (vm=0x7ffff77668c0 <vlib_global_main>) at /home/localadmin/vpp.17.01.3/build-data/../vlib/vlib/main.c:1448
   #4  0x00007ffff74f26f0 in vlib_main (vm=0x7ffff77668c0 <vlib_global_main>, input=0x7fffb50d4fb0)
       at /home/localadmin/vpp.17.01.3/build-data/../vlib/vlib/main.c:1680
   #5  0x00007ffff7783a1d in thread0 (arg=140737345120448) at /home/localadmin/vpp.17.01.3/build-data/../vlib/vlib/unix/main.c:485
   #6  0x00007ffff600ac1c in clib_calljmp () at /home/localadmin/vpp.17.01.3/build-data/../vppinfra/vppinfra/longjmp.S:110
   #7  0x00007fffffffd2f0 in ?? ()
   #8  0x00007ffff7783e7d in vlib_unix_main (argc=45, argv=0x1239b00) at /home/localadmin/vpp.17.01.3/build-data/../vlib/vlib/unix/main.c:545
   #9  0x0000000000c2b194 in main (argc=45, argv=0x1239b00) at /home/localadmin/vpp.17.01.3/build-data/../vpp/vnet/main.c:271
   (gdb)
