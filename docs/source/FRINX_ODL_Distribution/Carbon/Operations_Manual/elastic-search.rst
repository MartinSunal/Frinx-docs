
Elasticsearch
=============

* `Installation <#installation>`__
* `Configuration <#configuration>`__

  * `Configure Log4j <#configure-log4j>`__
  * `Configure Logstash <#configure-logstash>`__

* `Operation <#operation>`__

Installation
------------

1. If you have not already done so, `download the FRINX ODL distribution <https://frinx.io//downloads>`__ and :doc:`install it <running-frinx-odl-initial>`
2. `Install Elasticsearch <https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html>`__

In the unpackaged folder, start elasticsearch with

.. code-block:: guess

   ./bin/elasticsearch



3. `Install Kibana <https://www.elastic.co/downloads/kibana>`__ - download the version appropriate to your system. For the Linuz-64 bit tar.gz download file, unpackage it with

.. code-block:: guess

   tar -xvf filename



In the unpackaged folder, start kibana with

.. code-block:: guess

   ./bin/kibana



4. `Install logstash <https://www.elastic.co/downloads/logstash>`__ - which we'll use for collecting and parsing log files. It can transform an unstructured log into something meaningful and searchable.

For the Linuz-64 bit tar.gz download file, unpackage it with

.. code-block:: guess

   tar -xvf filename



Configuration
-------------

The base configuration is to use log4j socket listener for Logstash and the log4j socket appender in ODL FRINX.

Configure Log4j
~~~~~~~~~~~~~~~

Within the home directory of your FRINX ODL distribution, go to the **etc** directory.
Backup your old config file:  

.. code-block:: guess

   cp org.ops4j.pax.logging.cfg org.ops4j.pax.logging.cfg.bkp


Copy :download:`org.ops4j.pax.logging.cfg <org.ops4j.pax.logging.cfg>` into the same folder. The root logger section (near the top) of this file has been adjusted to log to elastic search.

Configure Logstash
~~~~~~~~~~~~~~~~~~

We must now configure socket listener for Logstash:

From your logstash directory(the directory created from unpackaging the download file at the start of this guide), move into the config directory:

.. code-block:: guess

   cd config


Copy this template :download:`logstash.conf file <logstash.conf>` into that config directory.

Edit line 7 of logstash.conf to point to karaf_home/data/log/karaf.log (it is currently set to /mnt/karaf.log).

Put the :download:`odl file <odl>` in /mnt/patterns/ or whatever directory you choose to set in line 18 of logstash.conf. For more info on custom patterns please see https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html#_custom_patterns

For more info on logstash and log4j see the link `here <https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html>`__ and `here <https://www.elastic.co/guide/en/logstash/current/plugins-inputs-log4j.html>`__

We started elasticsearch and kibana after downloading them (see the start of this guide).

We now need to start logstash. Move to your main logstash folder:

.. code-block:: guess

   cd ..

The start logstash with

.. code-block:: guess

   ./bin/logstash -f config/frinx.conf

Operation
---------

We have already started elasticsearch, kibana, and logstash. Now start karaf as normal by going to your FRINX ODL Distribution main directory for example distribution-karaf-2.3.0.frinx.

Then type

.. code-block:: guess

   ./bin/karaf

All logging information is now logged to an Elasticsearch node though Logstash. This information can be analysed with Kibana. Open Kibana in a Web browser by going to http://localhost:5601

