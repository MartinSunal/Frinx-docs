
FRINX Machine starting procedures
=================================

Initiate the FRINX Machine GUI
------------------------------

Once the FRINX Machine is started, initiate your browser and open the following GUIs:


* localhost:5000 --> FRINX workflow GUI
* localhost:5601 --> Kibana (log and inventory access)
* localhost:8888 --> FRINXit (Command line interface for FRINX ODL)

After iniciating GUI environments, start with creating a new device in the inventory

Create a new device in the inventory
------------------------------------

In our first workflow we will create a device entry in our inventory via the FRINX workflow UI.  

Many customers will choose to import bulk data from other data sources like Excel or CSV files. Many data import tools exist for Elasticsearch (e.g. `https://github.com/knutandre/excelastic <https://github.com/knutandre/excelastic>`_\ ). The import of bulk data is out of scope for this use case. We rather focus on data input via our GUI to demonstrate a simple set of workflows.

Click on:


* Metadata

  * Workflow Defs

Then, search for the workflow called "INVENTORY_add_cli_device".
Select the given workflow.

Then, select the tab “Input” and fill out the form.


.. image:: image_5_1.png
   :target: image_5_1.png
   :alt: preview5


After you have filled out the form click on "Execute Workflow". You should see a similar response like the one shown below.


.. image:: image_6_1.png
   :target: image_6_1.png
   :alt: preview6


After successful execution of our first workflow, we can see the the new device created in Elasticsearch. 


.. image:: image_7_1.png
   :target: image_7_1.png
   :alt: preview7


Mount the new device in FRINX OpenDaylight
------------------------------------------

Next we want to mount the device in ODL. The mount operation for NETCONF or CLI devices in ODL results in a permanent connection that is established, maintained and if necessary re-established with the device. Once a device is mounted in FRINX OpenDaylight, it can be accessed via the UniConfig framework for reading and writing configuration and operational data. The next workflow will mount the device in FRINX ODL. 

Click on: 


* Metadata 

  * Workflow Defs

Then, select the workflow to mount a single device: “SOUTHBOUND_mount_cli_from_inventory”

If you want to mount multiple devices from the inventory, choose the workflow **SOUTHBOUND_mount_all_cli_from_inventory**. 


.. image:: image_8_1.png
   :target: image_8_1.png
   :alt: preview8


Fill out the ID of the device for a single device or fill out the optional field "type" to specify which type of device you want to mount (e.g. “ios”, “io xr”, ...). 

In our case the ID is “ASR9000” to mount a single device. 

After you clicked on “Execute workflow” you should see a similar view like the one below:


.. image:: image_9_1.png
   :target: image_9_1.png
   :alt: preview9


Now we will look how the workflows are executing and how we can check if they have succeeded. 

The workflow that we have executed will spawn a number of sub workflows and will only show completed if all sub workflows have completed successfully. You can verify the state of main and sub-workflows in the view below. 

Click on to see the status of each workflow:


* Executions

  * All


.. image:: image_10_1.png
   :target: image_10_1.png
   :alt: preview10


In this view, we can see that all workflows have completed successfully. After the main workflow was executed it has spawned of multiple sub workflows until the last workflow checks if the device was successfully mounted. 

The following screenshots show additional information about the sub-workflows that is relevant to analysis and troubleshooting. 


.. image:: image_12_1.png
   :target: image_12_1.png
   :alt: preview12



.. image:: image_13_1.png
   :target: image_13_1.png
   :alt: preview13



.. image:: image_14_1.png
   :target: image_14_1.png
   :alt: preview14



.. image:: image_15_1.png
   :target: image_15_1.png
   :alt: preview15


Once the main workflow has successfully completed the device is mounted and can now be used to get information from the device or configure the device.
