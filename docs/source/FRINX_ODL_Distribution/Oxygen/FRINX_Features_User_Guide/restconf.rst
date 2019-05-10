
RESTCONF
========

RESTCONF operations overview
----------------------------

RESTCONF allows access to datastores in the controller.
There are two datastores:

**Config**: Contains data inserted via the controller  
**Operational**: Contains other data  

**Note**: Each request must start with the URI /restconf. RESTCONF listens on port 8080 for HTTP requests.

RESTCONF supports **OPTIONS**, **GET**, **PUT**, **POST**, and **DELETE** operations. Request and response data can either be in the XML or JSON format. 

XML structures according to yang are defined at: `XML-YANG <https://tools.ietf.org/html/rfc6020>`__. 

JSON structures are defined at: `JSON-YANG <https://tools.ietf.org/html/draft-lhotka-netmod-yang-json-02>`__. 

Data in the request must have a correctly set Content-Type field in the http header with the allowed value of the media type. 
The media type of the requested data has to be set in the Accept field. Get the media types for each resource by calling the OPTIONS operation. 
Most of the paths of the pathsRestconf endpoints use `Instance Identifier <https://wiki.opendaylight.org/view/OpenDaylight_Controller:MD-SAL:Concepts#Instance_Identifier>`__. 

**<identifier&gt>** is used in the explanation of the operations.

<identifier>
------------

*  This must start with <moduleName&gt>:<nodeName&gt>> where <moduleName> is a name of the module and <nodeName> is the name of a node in the module. It is sufficient to just use <nodeName> after <moduleName&gt>:<nodeName&gt>. Each <nodeName> has to be separated by.

* <nodeName> can represent a data node which is a list or container yang built-in type. If the data node is a list, there must be defined keys of the list behind the data node name, for example, <nodeName&gt>/<valueOfKey1>/<valueOfKey2>.

*  The format <moduleName>:<nodeName> has to be used in this case as well. Module A has node A1. Module B augments node A1 by adding node X. Module C augments node A1 by adding node X. For clarity, it has to be known which node is X (for example: C:X). For more details about encoding, see: `RESTCONF 02 - Encoding YANG Instance Identifiers in the Request URI <https://tools.ietf.org/html/draft-bierman-netconf-restconf-02#section-5.3.1>`__.

Mount point
-----------

A Node can be behind a mount point. In this case, the URI has to be in the format <identifier>/yang-ext:mount/<identifier>. The first <identifier> is the path to a mount point and the second <identifier> is the path to a node behind the mount point. A URI can end in a mount point itself by using <identifier>/yang-ext:mount.
More information on how to actually use mountpoints is available at: `OpenDaylight Controller:Config:Examples:Netconf <https://wiki.opendaylight.org/view/OpenDaylight_Controller:Config:Examples:Netconf>`__.

HTTP methods
------------

OPTIONS /restconf
~~~~~~~~~~~~~~~~~


* Returns the XML description of the resources with the required request and response media types in Web Application Description Language (WADL)

GET /restconf/config/<identifier>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


* Returns a data node from the Config datastore.
* <identifier> points to a data node which must be retrieved.

GET /restconf/operational/<identifier>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


* Returns the value of the data node from the Operational datastore.
* <identifier> points to a data node which must be retrieved.

PUT /restconf/config/<identifier>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


* Updates or creates data in the Config datastore and returns the state about success.
* <identifier> points to a data node which must be stored.

Example:  

.. code-block:: guess

   PUT http://<controllerIP>:8080/restconf/config/module1:foo/bar
   Content-Type: applicaton/xml
   <bar>
     …
   </bar>

Example with mount point:  

.. code-block:: guess

   PUT http://<controllerIP>:8080/restconf/config/module1:foo1/foo2/yang-ext:mount/module2:foo/bar
   Content-Type: applicaton/xml
   <bar>
     …
   </bar>

POST /restconf/config
~~~~~~~~~~~~~~~~~~~~~

Creates the data if it does not exist  

Example:  

.. code-block:: guess

   POST URL: http://localhost:8080/restconf/config/
   content-type: application/yang.data+json
   JSON payload:

      {
        "toaster:toaster" :
        {
          "toaster:toasterManufacturer" : "General Electric",
          "toaster:toasterModelNumber" : "123",
          "toaster:toasterStatus" : "up"
        }
     }

POST /restconf/config/<identifier>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Creates the data if it does not exist in the Config datastore, and returns the state about success.
* <identifier> points to a data node where data must be stored.
* The root element of data must have the namespace (data are in XML) or module name (data are in JSON.)

Example:  

.. code-block:: guess

   POST http://<controllerIP>:8080/restconf/config/module1:foo
   Content-Type: applicaton/xml/
   <bar xmlns=“module1namespace”>
     …
   </bar>

Example with mount point:  

.. code-block:: guess

   http://<controllerIP>:8080/restconf/config/module1:foo1/foo2/yang-ext:mount/module2:foo
   Content-Type: applicaton/xml
   <bar xmlns=“module2namespace”>
     …
   </bar>

POST /restconf/operations/<moduleName>:<rpcName>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Invokes RPC
* <moduleName>:<rpcName> - <moduleName> is the name of the module and <rpcName> is the name of the RPC in this module
* The Root element of the data sent to RPC must have the name “input”  
* The result can be the status code or the retrieved data having the root element “output”  

Example:  

.. code-block:: guess

   POST http://<controllerIP>:8080/restconf/operations/module1:fooRpc
   Content-Type: applicaton/xml
   Accept: applicaton/xml
   <input>
     …
   </input>

The answer from the server could be:  

.. code-block:: guess

   <output>
     …
   </output>

An example using a JSON payload:  

.. code-block:: guess

   POST http://localhost:8080/restconf/operations/toaster:make-toast
   Content-Type: application/yang.data+json
   {
     "input" :
     {
        "toaster:toasterDoneness" : "10",
        "toaster:toasterToastType":"wheat-bread"
     }
   }

*Note
Even though this is a default for the toasterToastType value in the yang, you still need to define it.*  

DELETE /restconf/config/<identifier>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


* Removes the data node in the Config datastore and returns the state about success.
* <identifier> points to a data node which must be removed.
  More information is available in the `RESTCONF RFC <https://tools.ietf.org/html/draft-bierman-netconf-restconf-02>`__.

Credit for the content of this page goes to the OpenDaylight community. The page content is take from `here <http://docs.opendaylight.org/en/stable-oxygen/developer-guide/controller.html?highlight=restconf#opendaylight-controller-md-sal-restconf>`__
