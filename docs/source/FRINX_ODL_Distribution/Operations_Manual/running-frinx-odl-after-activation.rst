
Operating the FRINX ODL Distribution
====================================

* `Operating in 'regular' mode (karaf in the foreground, with console) <#operating-in-regular-mode-karaf-in-the-foreground-with-console>`__

  * `Starting in regular mode <#starting-in-regular-mode>`__
  * `Stopping in regular mode <#stopping-in-regular-mode>`__

* `Operating in the background <#operating-in-the-background>`__

  * `Starting in background <#starting-in-background>`__
  * `Confirming karaf is running in background <#confirming-karaf-is-running-in-background>`__
  * `Connecting to the background process <#connecting-to-the-background-process>`__
  * `Stopping in background <#stopping-in-background>`__

* `Operating in 'server' mode (karaf in the foreground, without the console) <#operating-in-server-mode-karaf-in-the-foreground-without-the-console>`_

  * `Starting in server mode <#starting-in-server-mode>`__
  * `Confirming karaf is running <#confirming-karaf-is-running>`__
  * `Stopping the background process <#stopping-the-background-process>`__

* `Resetting FRINX ODL to a clean state <#resetting-frinx-odl-to-a-clean-state>`__
* `Setting JAVA_HOME and other variables <#setting-java-home-and-other-variables>`__

After running for the first time and generating a local license file, you no longer need to provide a token when starting karaf.

This page describes how you can operate the Frinx ODL distribution in different modes. The commands have been tested in CentOS, Ubuntu 16.04 and Ubuntu 18.04.

Note: Frinx ODL needs approximately 3 minutes to startup and shutdown.  

To maintain system integrity, **please do not interrupt the startup and shutdown processes** within this time.  

In the event of interruption, the initial state can be restored by entering the following commands from a terminal within your Frinx ODL main directory.

**First** command forcibly kills the Frinx ODL karaf process
**Second** command cleans certain directories:

.. code-block:: text

   kill -9 $(pgrep  -o -f  karaf)
   rm  -rf  data/ snapshots/ journal/

Operating in 'regular' mode (karaf in the foreground, with console)
-------------------------------------------------------------------

Starting in regular mode
~~~~~~~~~~~~~~~~~~~~~~~~

In your Frinx ODL Distribution directory, for example /home/username/distribution-karaf-3.1.0.frinx, type

.. code-block:: text

   ./bin/karaf


Stopping in regular mode
~~~~~~~~~~~~~~~~~~~~~~~~

To stop from within the karaf console there are three options:


1. Hold the 'CTRL' key and press the 'd' key
2. Type:

.. code-block:: text

       halt

3. Type:

.. code-block:: text

       shutdown

When prompted to confirm, type 'yes'.

Operating in the background
---------------------------

Starting in background
~~~~~~~~~~~~~~~~~~~~~~

In your Frinx ODL Distribution directory, for example /home/username/distribution-karaf-3.1.0.frinx, type

.. code-block:: text

       ./bin/start

This starts the Frinx ODL karaf process.

Confirming karaf is running in gackground
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Type

.. code-block:: text

       ./bin/status

Connecting to the background process
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Type

.. code-block:: text

       ./bin/client

By default, client tries to connect on localhost, on port 8101 (the default Apache Karaf SSH port).
Client accepts different options to let you connect on a remote Apache Karaf instance.  

You can use ``--help`` command to get details about these options.

or use command below

.. code-block:: text

       ssh karaf@localhost -p 8101

**Password**: karaf

This connection can be local or remote.

When connected to the background process, you can logout (this closes only the ./bin/client process, but not the FRINX ODL server) by typing

.. code-block:: text

       logout

When connected to the background process, you can shutdown the FRINX ODL server by typing  

.. code-block:: text

       shutdown

Stopping in background
~~~~~~~~~~~~~~~~~~~~~~

Type

.. code-block:: text

       ./bin/stop

Operating in 'server' mode (karaf in the foreground, without the console)
-------------------------------------------------------------------------

Starting in server mode
~~~~~~~~~~~~~~~~~~~~~~~

Start in your FRINX ODL Distribution directory, for example /home/username/distribution-karaf-3.1.0.frinx, type:

.. code-block:: text

       ./bin/karaf server &

Confirming karaf is running
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Type

.. code-block:: text

       ./bin/status

Stopping the background process
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

With karaf running in the background (from using ./bin/start), stop it from within a terminal by typing

.. code-block:: text

       ./bin/stop

Resetting FRINX ODL to a clean state
------------------------------------

The ODL distribution directory example: /home/username/distribution-karaf-3.1.0.frinx  

To 'reset' your distribution to a clean state and to delete any features previously installed, within your FRINX ODL distribution directory use the following command:

.. code-block:: text

       rm -rf data/ cache/ journal/ snapshots/

Setting JAVA_HOME and other variables
-------------------------------------

Edit the 'setenv' file in the bin directory within your FRINX ODL Distribution directory.
Uncomment the relevant line and set the variable as required:  

*E.g. to set the location of your Java home directory, uncomment the JAVA_HOME variable and point it to the appropriate folder depending on your Java installation*\ :

.. code-block:: text

       export JAVA_HOME=/opt/jdk1.8.0_151
