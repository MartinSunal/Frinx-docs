
Proxy server configuration
==========================


.. raw:: html

*  `Proxy settings <#proxy-settings>`__
*  `Proxy control <#proxy-control>`__

This part describes how to set up the SBE proxy server if you provide more SBE instances on more hosts. SBE proxy server is a reverse proxy server provided by NGINX.

**Figure 1: Multi host provision** 

.. image:: 3-multi-host.png
   :target: 3-multi-host.png
   :alt: Figure1: Multi host providing


Proxy settings
--------------

All proxy rules and options are defined in the **sbe.proxy** file in sbe project root.

.. code-block:: guess

   PROXY_HOST="localhost" -> Host or FQDN where is proxy provided PROXY_PORT=80 -> Port where is proxy listen

   PROXY_RULES_WEB=(

           "redmine.localhost=http://redmine.host1/"       -> **Transport rule for HTTP**
           "gerrit.localhost=http://gerrit.host1/"
           "jenkins.localhost=http://jenkins.host1/"
           "nexus.localhost=http://nexus.host1/"

           "redmine.titan.localhost=http://redmine.host2/"
           "gerrit.titan.localhost=http://gerrit.host2/"
           "jenkins.titan.localhost=http://jenkins.host2/"
           "nexus.titan.localhost=http://nexus.host2/"


   )

       PROXY_RULES_SSH=(

           "git.localhost:29418=host1:29418"           -> **Transport rule for SSH**
           "git.titan.localhost:29418=host2:29418"


   )



Proxy control
-------------

Start proxy server

.. code-block:: guess

   ./sbe proxy-start



Stop proxy server

.. code-block:: guess

   ./sbe proxy-stop



Restart proxy server

.. code-block:: guess

   ./sbe proxy-restart
