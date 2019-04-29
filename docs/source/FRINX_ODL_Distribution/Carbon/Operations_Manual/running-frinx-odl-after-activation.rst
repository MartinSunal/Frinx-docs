
Operating the FRINX ODL Distribution
====================================

* `Operating in 'regular' mode (karaf in the foreground, with console) <#operating-in-regular-mode-karaf-in-the-foreground-with-console>`__

  * `Starting <#starting>`__
  * `Stopping <#stopping>`__

* `Operating in the background <#operating-in-the-background>`__

  * `Starting <#starting-1>`__
  * `Confirming karaf is running <#confirming-karaf-is-running>`__
  * `Connecting to the background process <#connecting-to-the-background-process>`__
  * `Stopping <#stopping-1>`__

* `Operating in 'server' mode (karaf in the foreground, without the console) <#operating-in-server-mode-karaf-in-the-foreground-without-the-console>`_

  * `Starting <#starting-2>`__
  * `Confirming karaf is running <#confirming-karaf-is-running-1>`__
  * `Stopping the background process <#stopping-the-background-process>`__

* `Resetting FRINX ODL to a clean state <#resetting-frinx-odl-to-a-clean-state>`__
* `Setting JAVA_HOME and other variables <#setting-java_home-and-other-variables>`__

After running for the first time and generating a local license file, you no longer need to provide a token when starting karaf.

This page describes how you can operate the Frinx ODL distribution in different modes. The commands have been tested in CentOS, Ubuntu 16.04 and Ubuntu 18.04.

Note: Frinx ODL needs approximately 3 minutes to startup and shutdown.  

To maintain system integrity, **please do not interrupt the startup and shutdown processes** within this time.  

In the event of interruption, the initial state can be restored by entering the following commands from a terminal within your Frinx ODL main directory.

**First** command forcibly kills the Frinx ODL karaf process
**Second** command cleans certain directories:

.. code-block:: guess

   kill -9 $(pgrep  -o -f  karaf)
   rm  -rf  data/ snapshots/ journal/

Operating in 'regular' mode (karaf in the foreground, with console)
-------------------------------------------------------------------

Starting
~~~~~~~~

In your Frinx ODL Distribution directory, for example /home/username/distribution-karaf-3.1.0.frinx, type

.. code-block:: guess

   ./bin/karaf


Stopping
~~~~~~~~

To stop from within the karaf console there are three options:


1. Hold the 'CTRL' key and press the 'd' key
2. Type:

.. code-block:: guess

       halt

3. Type:

   .. code-block:: guess

       shutdown

When prompted to confirm, type 'yes'.

Operating in the background
---------------------------

Starting
~~~~~~~~

In your Frinx ODL Distribution directory, for example /home/username/distribution-karaf-3.1.0.frinx, type

.. code-block:: guess

       ./bin/start

This starts the Frinx ODL karaf process.

Confirming karaf is running
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Type

.. code-block:: guess

       ./bin/status

Connecting to the background process
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Type

.. code-block:: guess

       ./bin/client

By default, client tries to connect on localhost, on port 8101 (the default Apache Karaf SSH port).
Client accepts different options to let you connect on a remote Apache Karaf instance.  

You can use ``--help`` command to get details about these options.

or use command below

.. code-block:: guess

       ssh karaf@localhost -p 8101

**Password**: karaf

This connection can be local or remote.

When connected to the background process, you can logout (this closes only the ./bin/client process, but not the FRINX ODL server) by typing

.. code-block:: guess

       logout

When connected to the background process, you can shutdown the FRINX ODL server by typing  

.. code-block:: guess

       shutdown

Stopping
~~~~~~~~

Type

.. code-block:: guess

       ./bin/stop

Operating in 'server' mode (karaf in the foreground, without the console)
-------------------------------------------------------------------------

Starting
~~~~~~~~

Start in your FRINX ODL Distribution directory, for example /home/username/distribution-karaf-3.1.0.frinx, type:

.. code-block:: guess

       ./bin/karaf server &

Confirming karaf is running
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Type

.. code-block:: guess

       ./bin/status

Stopping the background process
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

With karaf running in the background (from using ./bin/start), stop it from within a terminal by typing

.. code-block:: guess

       ./bin/stop

Resetting FRINX ODL to a clean state
------------------------------------

The ODL distribution directory example: /home/username/distribution-karaf-3.1.0.frinx  

To 'reset' your distribution to a clean state and to delete any features previously installed, within your FRINX ODL distribution directory use the following command:

.. code-block:: guess

       rm -rf data/ cache/ journal/ snapshots/

Setting JAVA_HOME and other variables
-------------------------------------

Edit the 'setenv' file in the bin directory within your FRINX ODL Distribution directory.
Uncomment the relevant line and set the variable as required:  

*E.g. to set the location of your Java home directory, uncomment the JAVA_HOME variable and point it to the appropriate folder depending on your Java installation*\ :

.. code-block:: guess

       export JAVA_HOME=/opt/jdk1.8.0_151
