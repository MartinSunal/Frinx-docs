
Swagger
=======

Swagger is a framework backed by a large ecosystem of tools that helps developers to work with RESTful Web services.  The Swagger toolset includes support for automated documentation, code generation, and test-case generation.

Following files provide OpenAPI files for FRINX ODL's REST interface (in context of uniconfig topology, unified topology and southbound topology) which can be used with Swagger tools.

----

Uniconfig REST API documented with OpenAPI v2
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


* OpenAPI document generated from Uniconfig model + Openconfig models

**Download** document here:

`https://license.frinx.io/download/swagger-uniconfig-4.2.0.frinx.zip <https://license.frinx.io/download/swagger-uniconfig-4.2.0.frinx.zip>`__

----

Uniconfig client code generated from OpenAPI definition available for Python and Go clients
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


* Client code library, encapsulating REST calls no available for external applications interacting with Uniconfig

**Download** Python code library: 

`https://license.frinx.io/download/swagger-uniconfig-python-4.2.0.frinx.zip <https://license.frinx.io/download/swagger-uniconfig-python-4.2.0.frinx.zip>`__

**Download** Go code library: 

`https://license.frinx.io/download/swagger-uniconfig-go-4.2.0.frinx.zip <https://license.frinx.io/download/swagger-uniconfig-go-4.2.0.frinx.zip>`__

----

Unified REST API documented with OpenAPI v2
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


* OpenAPI document generated from Unified topology model + Openconfig models

**Download** document here:

`https://license.frinx.io/download/swagger-unified-4.2.0.frinx.zip <https://license.frinx.io/download/swagger-unified-4.2.0.frinx.zip>`__

----

Unified client code generated from OpenAPI definition available for Python and Go clients
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


* Client code library, encapsulating REST calls no available for external applications interacting with unified topology

**Download** Python code library: 

`https://license.frinx.io/download/swagger-unified-python-4.2.0.frinx.zip <https://license.frinx.io/download/swagger-unified-python-4.2.0.frinx.zip>`__

**Download** Go code library: 

`https://license.frinx.io/download/swagger-unified-go-4.2.0.frinx.zip <https://license.frinx.io/download/swagger-unified-go-4.2.0.frinx.zip>`__

----

Southbound REST API documented with OpenAPI v2
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


* OpenAPI document generated from Cli tipology + Netconf topology models

**Download** document here:

`https://license.frinx.io/download/swagger-southbound-4.2.0.frinx.zip <https://license.frinx.io/download/swagger-southbound-4.2.0.frinx.zip>`__

----

Southbound client code generated from OpenAPI definition available for Python and Go clients
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


* Client code library, encapsulating REST calls no available for external applications interacting with southbound (cli and netconf topology)

**Download** Python code library:

`https://license.frinx.io/download/swagger-southbound-python-4.2.0.frinx.zip <https://license.frinx.io/download/swagger-southbound-python-4.2.0.frinx.zip>`__

**Download** Go code library: 

`https://license.frinx.io/download/swagger-southbound-go-4.2.0.frinx.zip <https://license.frinx.io/download/swagger-southbound-go-4.2.0.frinx.zip>`__

----

Example LACP service implementation using generated Swagger based client code can be found in the link below:

`https://github.com/FRINXio/Lacp-service-labdocs <https://github.com/FRINXio/Lacp-service-labdocs>`__

How to launch Swagger-UI
------------------------


1. Download swagger-uniconfig-4.2.0.frinx.zip (The first download link on top)

2. Unzip the file

3. Go to the directory which you extracted the file in

4. Run following command in your terminal:

.. code-block:: text

   sudo docker run -p 80:8080 -e SWAGGER_JSON=/foo/uniconfig.yaml -v $PWD:/foo swaggerapi/swagger-ui

5. Open your browser and visit: localhost
