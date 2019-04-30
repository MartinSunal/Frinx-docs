
Configuring logging level within karaf
======================================

* `Configuring logging level within karaf <#configuring-logging-level-within-karaf>`__

  * `Check logging level <#check-logging-level>`__
  * `Add or change logging level for a particular feature <#add-or-change-logging-level-for-a-particular-feature>`__

It's possible to adjust the verbosity of logging for each FRINX ODL feature.
The following levels are available (most verbose listed first) - TRACE, DEBUG, WARN, INFO, ERROR.
INFO is less verbose and is a good compromise in terms of verbosity and effectiveness.  

Check logging level
-------------------

After :doc:`starting FRINX ODL <running-frinx-odl-after-activation>`, first check the logging level currently set for each feature:  

Within the karaf console type:

.. code-block:: guess

   log:list

This will display output similar to the following:

.. code-block:: guess

   Logger                                                           | Level
   ------------------------------------------------------------------------
   ROOT                                                             | INFO
   io.frinx                                                         | DEBUG
   org.apache.karaf.features                                        | DEBUG
   org.opendaylight.controller.cluster                              | DEBUG
   org.opendaylight.controller.config.yang.md.sal.connector.netconf | INFO
   org.opendaylight.daexim                                          | DEBUG
   org.opendaylight.netconf.sal.connect                             | INFO
   org.opendaylight.netconf.topology                                | INFO

Add or change logging level for a particular feature
----------------------------------------------------

To add or change the logging level for a particular feature e.g. io.frinx.cli type

.. code-block:: guess

   log:set INFO io.frinx.cli

You will see the logging level has been updated when you again type

.. code-block:: guess

   log:list

The output will now be similar to the following, with the level for io.frinx.cli having been changed from DEBUG to INFO:

.. code-block:: guess

   Logger                                                           | Level
   ------------------------------------------------------------------------
   ROOT                                                             | INFO
   io.frinx                                                         | DEBUG
   io.frinx.cli                                                     | INFO
   org.apache.karaf.features                                        | DEBUG
   org.opendaylight.controller.cluster                              | DEBUG
   org.opendaylight.controller.config.yang.md.sal.connector.netconf | INFO
   org.opendaylight.daexim                                          | DEBUG
   org.opendaylight.netconf.sal.connect                             | INFO
   org.opendaylight.netconf.topology                                | INFO

To begin viewing the log type:

.. code-block:: guess

   log:tail

Karaf logs are also written to the data/log directory within your main FRINX ODL distribution directory so can be viewed outside of karaf.

Default settings for logging can be changed in etc/org.ops4j.pax.logging.cfg
within your main FRINX ODL distribution directory.
