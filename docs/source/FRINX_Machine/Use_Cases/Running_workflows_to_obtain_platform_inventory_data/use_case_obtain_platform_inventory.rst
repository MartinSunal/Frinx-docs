
Running workflows to obtain platform inventory data
===================================================

In this section we show how users can execute workflows to obtain platform inventory data from devices in the network and to store them in the inventory (Elasticsearch).

The goal of this use case is to collect inventory information about physical devices via their vendor specific NETCONF or CLI interfaces, convert this information into OpenConfig data structures and store the resulting information as a child entry to its associated parent in Elasticsearch. 

The outcome is that users can manage their physical network inventory (line cards, route processors, modules, transceivers, etc …) across different hardware vendors in real-time via a single uniform interface.

Collect platform information from the device and store in the inventory
-----------------------------------------------------------------------

In the next step we will execute a workflow that collects platform information from every mounted device, converts the vendor specific information into OpenConfig format and writes the resulting data to the inventory.

Click on:


* Metadata

  * Workflow Defs

Then select the workflow:
**PLATFORM_read_components_all_from_unified_update_inventory**


.. image:: image_16.png
   :target: image_16.png
   :alt: preview10


Once selected, you can execute the workflow without providing additional information.


.. image:: image_17.png
   :target: image_17.png
   :alt: preview10


Under


* Executions

  * All

You can see the progress of the workflow, input/output data of each task and statistics associated with the workflow execution.


.. image:: image_18.png
   :target: image_18.png
   :alt: preview10



.. image:: image_19.png
   :target: image_19.png
   :alt: preview10



.. image:: image_20.png
   :target: image_20.png
   :alt: preview10



.. image:: image_21.png
   :target: image_21.png
   :alt: preview10


After the main and sub-workflows have completed successfully the platform information is now stored in the inventory as a child entry to the device ID that the information comes from.


.. image:: image_22.png
   :target: image_22.png
   :alt: preview10


The execution of all workflows can be manually, via the UI, or can be automated and scheduled via the REST API of conductor server.
