*This feature was deprecated in FRINX 2.3.1. For using Elasticsearch with FRINX ODL logs see `here <../Operations_Manual/elastic-search.md>`_\ *


Elasticsearch with TSDR datastore
=================================

*  `Setting Up the environment <#setting-up-the-environment>`__
*  `Creating a custom elasticsearch docker image <#creating-a-custom-elasticsearch-docker-image>`__
*  `Running the custom elasticsearch plugin <#running-the-custom-elasticsearch-plugin>`__
*  `Setting up the Frinx distribution <#setting-up-the-frinx-distribution>`__
*  `Testing the setup <#testing-the-setup>`__
*  `Installing the necessary features to the Frinx distribution <#installing-the-necessary-features-to-the-frinx-distribution>`__
*  `Setting up mininet <#setting-up-mininet>`__

Setting Up the environment
--------------------------

To setup and run the Frinx TSDR data store elasticsearch plugin, you need to have an elasticsearch node (or a cluster of such nodes) running. We will use a customized elasticsearch docker image for this purpose.

Your elasticsearch setup must have the Delete By Query Plugin installed. Without this, some of the elk functionality won't work properly.

*Note: for the remainder of this document, we will use elk to refer to the TSDR data store elasticsearch plugin.*

Creating a custom elasticsearch docker image
--------------------------------------------

(You can skip this section if you already have an instance of elasticsearch running) Run the following set of commands:

.. code-block:: guess

   cat << EOF > Dockerfile
   FROM elasticsearch:2

   RUN /usr/share/elasticsearch/bin/plugin install --batch delete-by-query
   EOF



To build the image, run the following command in the directory where the Dockerfile was created:

.. code-block:: guess

   docker build -t elasticsearch-dd .



You can check whether the image was properly created by running:

.. code-block:: guess

   docker images

This should print all your container images including the elasticsearch-dd.

Running the custom elasticsearch plugin
---------------------------------------

Now we can create and run a container from our image by typing:

.. code-block:: guess

   docker run -d -p 9200:9200 -p 9300:9300 --name elk-dd elasticsearch-dd

To see whether the container is running, run the following command:

.. code-block:: guess

   docker ps

The output should include a row with elk-dd in the NAMES column. To check the std out of this container use

.. code-block:: guess

   docker logs elk-dd



Setting up the Frinx distribution
---------------------------------

The next step is to install all the necessary dependencies in the running Frinx distribution. To do so, in the running distribution console type:

.. code-block:: guess

   feature:install odl-tsdr-elasticsearch



Once the feature has been installed, you can change some of its properties. For  example, to setup the url where your elasticsearch installation runs,  change the serverUrl parameter in the tsdr-persistence-elasticsearch.properties file.

All the data are stored into the tsdr index under a type. The metric data are  stored under the metric type and the log data are store under the log type. You can modify the \ **tsdr-persistence-elasticsearch_metric_mapping.json**  or the  **tsdr-persistence-elasticsearch_log_mapping.json**  file to change or tune the  mapping for those types. The changes in those files will be promoted after the feature is reloaded or the distribution is restarted.

All the configuration files are located int the FRINX ODL **etc**  directory.

Testing the setup
-----------------

We can now test whether the setup is correct by downloading and installing mininet, which we use to send some data to the running elasticsearch instance.

Installing the necessary features to the Frinx distribution
-----------------------------------------------------------

The distribution has to be able to process the data sent by the OpenFlow capable switch. In order to do so, we need to install two additional features:

.. code-block::

   feature:install odl-tsdr-openflow-statistics-collector

We can check whether the distribution is now listening on port 6653:

.. code-block::

   netstat -an | grep 6653

Note that in beryllium you will probably need to install odl-openflowplugin-all feature as well.

Setting up mininet
------------------

Follow these instructions to download and install mininet. We recommend the VM based solution.

After successfully booting up and running the mininet VM, run the following command to set up a small network:

.. code-block::

   sudo mn --topo single,3 --controller 'remote,ip=distro_ip,port=6653' --switch ovsk,protocols=OpenFlow13

where the distro_ip is the IP address of the machine where the Frinx distribution is running. This command will create three hosts connected to one OpenFlow capable switch.

We can check whether some data was stored in the elasticsearch by running the following command in the distribution console:

.. code-block::

   tsdr:list FLOWTABLESTATS

The output should look similar to the following:

.. code-block::

   [NID=openflow:1][DC=FLOWTABLESTATS][MN=ActiveFlows][RK=Node:openflow:1,Table:50][TS=1473427383598][0]
   [NID=openflow:1][DC=FLOWTABLESTATS][MN=PacketMatch][RK=Node:openflow:1,Table:50][TS=1473427383598][0]
   [NID=openflow:1][DC=FLOWTABLESTATS][MN=PacketLookup][RK=Node:openflow:1,Table:50][TS=1473427383598][0]
   [NID=openflow:1][DC=FLOWTABLESTATS][MN=ActiveFlows][RK=Node:openflow:1,Table:80][TS=1473427383598][0]
   [NID=openflow:1][DC=FLOWTABLESTATS][MN=PacketMatch][RK=Node:openflow:1,Table:80][TS=1473427383598][0]
   [NID=openflow:1][DC=FLOWTABLESTATS][MN=PacketMatch][RK=Node:openflow:1,Table:246][TS=1473427383598][0]

Or you can directly query elasticsearch:

.. code-block::

   curl -XPOST "http://elasticseach_ip:9200/_search?pretty" -d'{ "from": 0, "size": 10000, "query": { "match_all": {} } }'

The elasticseach_ip is the IP address of the server where the elasticsearch is running.


.. raw:: html

   <table>
     <thead>
       <tr>
         <th>
           Feature Guide
         </th>

         <th>
         </th>

         <th>
         </th>
       </tr>
     </thead>

     <tbody>
       <tr>
         <td>
           Feature introduced in
         </td>

         <td>
           Frinx 1.4.0
         </td>

         <td>
           Elasticsearch module
         </td>
       </tr>

       <tr>
         <td>
           Feature deprecated in
         </td>

         <td>
           Frinx 2.3.1
         </td>

         <td>
         </td>
       </tr>
     </tbody>
   </table>

