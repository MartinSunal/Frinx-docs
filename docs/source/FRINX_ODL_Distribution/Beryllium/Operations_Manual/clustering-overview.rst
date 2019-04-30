
Clustering Overview
===================

* `High availability (HA) <#high-availability-ha>`__
* `Data persistence <#data-persistence>`__
* `Scaling <#scaling>`__
* `Multiple node clustering <#multiple-node-clustering>`__

  * `a. Setting up <#a-setting-up>`__
  * `b. Info on clustering functionality <#b-info-on-clustering-functionality>`__

Clustering enables multiple instances of the Frinx ODL Distribution to work together as one entity. Data from the in-memory MD-SAL tree is split into smaller sub-trees (inventory, topology, and default) and distributed across the machines (referred to as nodes) which are part of the cluster.

This offers a number of benefits:

High availability (HA)
----------------------

If you have multiple instances of Frinx ODL running as a cluster and one crashes, you would still have the other instances working and available.

Data persistence
----------------

You will not lose any data gathered by Frinx ODL after a manual restart or a crash. After restarting, you can use the persisted data to reinstate shards to their previous state.

Scaling
-------

If you have multiple Frinx ODL instances running as a cluster, you can potentially do more work or store more data. 

Multiple node clustering
------------------------

a. Setting up
~~~~~~~~~~~~~

To run the Frinx ODL distribution in a three node cluster (that is, on three machines), do the following:
1. Determine the three machines (nodes) that will make up the cluster and copy the Frinx ODL distribution to each of those machines.  

2. Unzip the controller distribution.  

3. Run Karaf: In your ``{Frinx ODL main}`` directory, type

.. code-block:: guess

   ./bin/karaf 


4.\ In the karaf terminal, type:

.. code-block:: guess

   feature:install odl-mdsal-clustering


To check whether odl-mdsal-clustering has finished installing, type the following within the karaf terminal

.. code-block:: guess

   feature:list |grep odl-mdsal-clustering


Once installation is complete, an 'x' will be shown after the second column in the list.

5. Once the odl-mdsal-clustering feature installation is complete,  three configuration files will automatically be created in ``{Frinx ODL main}/configuration/initial/``. 

You do not need to edit these files. We will use a script to automatically configure two of these files (\ ``akka.conf`` and ``module-shards.conf``\ )

Change directory to ``{Frinx ODL main}/bin``

Type the following command, where the first argument is an index (1,2 or 3 - according to machine 1,2 or 3) and the final three arguments are the IP addresses (example IPs are used below - replace these with IPs of your machines) of the machines in the cluster (note when you run the command on each machine you list the same three IP addresses each time). On the first machine you would run:

.. code-block:: guess

   ./configure_cluster.sh 1 10.10.199.6 10.10.199.7 10.10.199.8


On the second machine you would run:

.. code-block:: guess

   ./configure_cluster.sh 2 10.10.199.6 10.10.199.7 10.10.199.8


On the third machine you would run:   

.. code-block:: guess

   ./configure_cluster.sh 3 10.10.199.6 10.10.199.7 10.10.199.8


When you restart Frinx ODL (on each machine), clustering will be active.
To restart from within the karaf terminal hold the 'CTRL' key and type the 'd' key.
Wait for three minutes. Then in the terminal window, still in the ``{Frinx ODL main}/bin`` directory, type

.. code-block:: guess

   ./karaf


Once karaf has fully started (which can take three minutes) you will be able to use any of the three member nodes (machines) to access the data residing in the datastore. For example, if you want to view information about the shard designated as *member-1* on a node, query the shard’s data by making the following HTTP request (we recommend using Postman): 

*HTTP Method: GET*  

*HTTP URL:* http://localhost:8181/jolokia/read/org.opendaylight.controller:Category=Shards,name=member-1-shard-inventory-config,type=DistributedConfigDatastore  

If prompted, enter admin as both the username and password.  

*HTTP: EXPECTED RESPONSE*
The request should return the following information:  

.. code-block:: json

   {  
      "timestamp":1410524741,
      "status":200,
      "request":{  
         "mbean":"org.opendaylight.controller:Category=Shards,name=member-1-shard-inventory-config,type=DistributedConfigDatastore",
         "type":"read"
      },
      "value":{  
         "ReadWriteTransactionCount":0,
         "LastLogIndex":-1,
         "MaxNotificationMgrListenerQueueSize":1000,
         "ReadOnlyTransactionCount":0,
         "LastLogTerm":-1,
         "CommitIndex":-1,
         "CurrentTerm":1,
         "FailedReadTransactionsCount":0,
         "Leader":"member-1-shard-inventory-config",
         "ShardName":"member-1-shard-inventory-config",
         "DataStoreExecutorStats":{  
            "activeThreadCount":0,
            "largestQueueSize":0,
            "currentThreadPoolSize":1,
            "maxThreadPoolSize":1,
            "totalTaskCount":1,
            "largestThreadPoolSize":1,
            "currentQueueSize":0,
            "completedTaskCount":1,
            "rejectedTaskCount":0,
            "maxQueueSize":5000
         },
         "FailedTransactionsCount":0,
         "CommittedTransactionsCount":0,
         "NotificationMgrExecutorStats":{  
            "activeThreadCount":0,
            "largestQueueSize":0,
            "currentThreadPoolSize":0,
            "maxThreadPoolSize":20,
            "totalTaskCount":0,
            "largestThreadPoolSize":0,
            "currentQueueSize":0,
            "completedTaskCount":0,
            "rejectedTaskCount":0,
            "maxQueueSize":1000
         },
         "LastApplied":-1,
         "AbortTransactionsCount":0,
         "WriteOnlyTransactionCount":0,
         "LastCommittedTransactionTime":"1969-12-31 16:00:00.000",
         "RaftState":"Leader",
         "CurrentNotificationMgrListenerQueueStats":[  

         ]
      }
   }

The key parameter from the above output is ShardName, whose structure is as follows:

.. code-block:: guess

   <member-name>-shard-<shard-name-as-per-configuration>-<store-type>  


These *Data shards* are used to house all or a certain segment of various types of Frinx ODL data. For example, one shard may contain all of a particular module’s inventory data while another shard contains all of its topology data. Each shard has replicas configured, which means the same data is stored on different nodes, ensuring data persistence in the event that one node becomes unoperational.

b. Info on clustering functionality
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

After a cluster 'node' (sometimes referred to as a 'member') is started, it sends a message to each other node within the cluster, which are referred to as 'seed' nodes. The cluster node then sends a join command to the first seed node that responds. If none of its seed nodes reply, the cluster member repeats this process until it successfully establishes a connection or is shutdown.

In the event that a node becomes unreachable, it remains down for a configurable period of time (10 seconds by default). Once a node goes down, you need to restart Frinx ODL on it so that it can rejoin the cluster. Once a restarted node joins a cluster, it will synchronize with the lead node automatically.  
