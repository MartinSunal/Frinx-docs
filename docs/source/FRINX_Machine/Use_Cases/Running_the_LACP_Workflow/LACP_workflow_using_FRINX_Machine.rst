
LACP workflow using FRINX Machine
=================================

This workflow is using uniconfig to create LAG interface on two nodes and assigns the bundle id to given interfaces on both nodes.

**Supported device**: ios-xr mounted as a cli device

Creating a link aggregation between two nodes
---------------------------------------------

In the next step we will create a link between node 1 and node 2.

Click on:


* Metadata

  * Workflow Defs

Then select the workflow: **Link_aggregation**


.. image:: select-the-workflow.png
   :target: select-the-workflow.png
   :alt: preview10


**Example of input parameters**:


.. image:: workflow-inputs.png
   :target: workflow-inputs.png
   :alt: preview10


After providing input parameters, you can execute the workflow.

Workflow execution
------------------

After workflow execution, you will be presented with representing diagram.


.. image:: Workflow-diagram-aggregation.jpg
   :target: Workflow-diagram-aggregation.jpg
   :alt: preview10


The workflow diagram in progress will color the steps according to the progress.


.. image:: Workflow-diagram-aggregation-6.2.jpg
   :target: Workflow-diagram-aggregation-6.2.jpg
   :alt: preview10


**NOTE**: Diagram with progress of the workflow can be updated by hiting F5 key.


.. image:: completed-lacp-workflow-diagram.png
   :target: completed-lacp-workflow-diagram.png
   :alt: preview10


Diagram displayed above shows the workflow has been successfully completed.
