
UniConfig Native
================

UniConfig native allows to communicate with network devices using their native YANG data models (e.g.: Cisco YANG models, JunOS Yang models, CableLabs YANG models, ...) to manage configurations. With UniConfig native, it is possible to mount NETCONF devices, sync configuration in their native format and manage those devices without the need to develop translation units.

Table of content
----------------

  *  `Activate your FRINX ODL Distribution <#activate-your-frinx-odl-distribution>`__
    *  `How to set JVM max memory <#how-to-set-jvm-max-memory>`__
  *  `UniConfig-native usage <#uniconfig-native-usage>`__
    *  `Usage for Cisco XR device <#usage-for-cisco-xr-device>`__
      *  `Device setup <#device-setup>`__
        *  `Set white list and black list <#set-white-list-and-black-list>`__
      *  `Mounting of a device <#mounting-of-a-device>`__
        *  `Check if the device is mounted succesfully <#check-if-the-device-is-mounted-succesfully>`__
        *  `Check availability of the command in UniConfig <#check-availability-of-the-command-in-uniconfig>`__
        *  `Check if an interface exists on a device <#chcek-if-an-interface-exists-on-a-device>`__
      *  `Sync configuration from network <#sync-configuration-from-network>`__
      *  `Add configuration to the config datastore <#add-configuration-to-the-config-datastore>`__
      *  `Commit configuration to the device <#commit-configuration-to-the-device>`__
      *  `Calculate diff <#calculate-diff>`__
      *  `Replace config with operational <#replace_config_with_operational>`__
      *  `Snapshot <#snapshot>`__
        *  `Create a snapshot <#create-a-snapshot>`__
        *  `Replace config with snapshot <#replace-config-with-snapshot>`__
        *  `Delete a snapshot <d#elete-a-snapshot>`__
      *  `Unmount device <#unmount-device>`__
    *  `Usage for Junos devices <#usage-for-junos-device>`__
      *  `Enable whitelist <#enable-whitelist>`__
      *  `Mount JunOS device <#mount-junos-device>`__
      *  `Show config <#show-config>`__
      *  `Enable interface in configuration <#enable-interface-in-configuration>`__
      *  `Disable interface in configuration <#disable-interface-in-configuration>`__
  
Activate your FRINX ODL Distribution
====================================

1. Activate the FRINX ODL Distribution

Follow the instructions `here <https://docs.frinx.io/FRINX_ODL_Distribution/Oxygen/Operations_Manual/running-frinx-odl-initial.html>`__

2. Run the FRINX ODL Distribution

Enter in the FRINX ODL Distribution directory, e.g.:

.. code-block:: guess

   cd  ./distribution-karaf-4.2.1.rc1-frinx

**NOTE**: Before running FRINX ODL it is recommended to set the Java Virtual Machine maximum memory. For small networks, 4GB Java Virtual Machine maximum memory is sufficient. For medium to large networks JVM max memory should be increased to 8GB or more.

How to set JVM max memory
-------------------------

Open the file:

.. code-block:: guess

   gedit ./bin/setenv

edit the variable to the desired value, e.g.:

.. code-block:: guess

   export JAVA_MAX_MEM="4G"

It is valid only in form of integer number.
**NOTE**: Do not forget to add memory unit symbol

“m” or “M” to specify MB otherwise
“g”  or “G” to specify GB

Run the FRINX ODL Distribution
==============================

To initiate FRINX ODL running on Karaf, use this command:

.. code-block:: guess

   ./bin/karaf

Install the features necessary to use UniConfig-native with command below:

.. code-block:: guess

   frinx-user@root>feature:install frinx-UniConfig-native frinx-unified-topology odl-netconf-topology

In alternative, in order to avoid to install the frinx fetures at every execution it is possible to insert them inside a configuration file. To do this open the configuration file:

.. code-block:: guess

   gedit ./etc/org.apache.karaf.features.cfg

Add to the variable “featuresBoot” the comma separated list of features to be installed, the variable in this case will looks like:

.. code-block:: guess

   featuresBoot = (config,standard,region,package,kar,ssh,management,odl-jolokia),(odl-daexim-all),frinx-uniconfig-native,frinx-unified-topology,odl-netconf-topology

Save and exit. 

Finally run:

.. code-block:: guess

   ./bin/karaf

To check if the desired features are properly installed run:

.. code-block:: guess

   frinx-user@root>feature:list

And check if the needed features have a ”x” in the “Required” column

Now FRINX ODL Distribution is properly set up to run UniConfig-native.

UniConfig-native usage
======================

Usage for Cisco XR device
-------------------------

Device setup
~~~~~~~~~~~~

Perform the following operations to mount a device in UniConfig native

Set white list and black list
+++++++++++++++++++++++++++++

In order to mount a device using UniConfig native, it is necessary to explicitly permit device types by whitelisting their capabilities. In addition, a blacklist ignores root configuration items when reading the configuration from the device.

To set the whitelist run:

.. code-block:: guess

   curl -X PUT \
     http://localhost:8181/restconf/config/direct-unit-matcher:direct-unit-matchers/direct-unit-matcher/xr \
     -H 'Accept: application/json' \
     -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
     -H 'Content-Type: application/json' \
     -d '{

       "direct-unit-matcher": [
    	   {
    	     "name": "xr",
    	     "capability-regex-matcher":[".*Cisco.*",".*cisco.*",".*openconfig.*", ".*ietf.*"]
    	   }
       ]

   }'

To set the blacklist run:

.. code-block:: guess

   curl -X PUT \
  http://localhost:8181/restconf/config/uniconfig-manager:blacklisted-reads/blacklisted-read/xr \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/json' \
  -d '{
    "blacklisted-read": {
        "matcher-ref": "xr",
        "paths": {
            "path": [
                "interfaces", "vlans"
            ]
        }
    }
}'

Mounting of a device
++++++++++++++++++++

Now, the device can be mounted with the following request

.. code-block:: guess

   curl -X PUT \
  http://localhost:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/R1 \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache' \
  -d '{
  "node": [
    {
      "node-id": "R1",
      "netconf-node-topology:host": "192.168.1.214",
      "netconf-node-topology:port": 830,
      "netconf-node-topology:keepalive-delay": 0,
      "netconf-node-topology:tcp-only": false,
      "netconf-node-topology:username": "USERNAME",
      "netconf-node-topology:password": "PASSWORD"
    }
  ]
}'

Caption:

**R1** is the name of the node
**192.168.1.214** is the ip address of the node
**830** is the port number of the node
**USERNAME** is the username to access the node
**PASSWORD** it is the respective password

Check if the device is mounted succesfully
##########################################

After the device has been mounted, the connection can be checked with the following command:

.. code-block:: guess

   curl -X GET \
  http://localhost:8181/restconf/operational/network-topology:network-topology/topology/topology-netconf/node/R1 \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache'

In case the device is still connecting console will return:

.. code-block:: guess

   {
    "node": [
        {
            "node-id": "R1",
            "netconf-node-topology:host": "192.168.1.214",
            "netconf-node-topology:connection-status": "connecting",
            "netconf-node-topology:port": 830
        }
    ]
   }

Send again the same GET request until the device will be connected.

When the device is connected, the response is similar to:

.. code-block:: guess

   {
    "node": [
        {
            "node-id": "R1",
            "netconf-node-topology:unavailable-capabilities": {
                "unavailable-capability": [
                    ...
                    {
                        "capability": "(http://openconfig.net/yang/bgp?revision=2015-05-15)bgp",
                        "failure-reason": "unable-to-resolve"
                    },
                    {
                        "capability": "(http://cisco.com/ns/yang/Cisco-IOS-XR-shellutil-filesystem-oper?revision=2015-11-09)Cisco-IOS-XR-shellutil-filesystem-oper-sub1",
                        "failure-reason": "missing-source"
                    },
                    ...                
                 ]
            },
            "netconf-node-topology:available-capabilities": {
                "available-capability": [
                    ...
                    {
                        "capability-origin": "device-advertised",
                        "capability": "urn:ietf:params:netconf:capability:confirmed-commit:1.1"
                    },
                                        {
                        "capability-origin": "device-advertised",
                        "capability": "(http://cisco.com/ns/yang/Cisco-IOS-XR-tty-server- oper?revision=2015-01-07)Cisco-IOS-XR-tty-server-oper"
                    },


                ...
                ]
            },
            "netconf-node-topology:host": "192.168.1.214",
            "netconf-node-topology:connection-status": "connected",
            "netconf-node-topology:port": 830
        }
    ]
   }

This response body shows which are the available capabilities that have been properly loaded and which are instead the unavailable capabilities that have not been loaded with the related failing reason.

Check availability of the command in UniConfig
##############################################

The following command checks that the configuration of the device is available in UniConfig:

.. code-block:: guess

   curl -X GET \
  http://localhost:8181/restconf/config/network-topology:network-topology/topology/UniConfig/node/R1/frinx-UniConfig-topology:configuration \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Cache-Control: no-cache' \
  -H 'Connection: keep-alive' \
  -H 'Content-Type: application/json' \
  -H 'Host: localhost:8181'

The example of output:

.. code-block:: guess

   {
    "frinx-UniConfig-topology:configuration": {
        "Cisco-IOS-XR-crypto-sam-cfg:crypto": {
            "Cisco-IOS-XR-crypto-ssh-cfg:ssh": {
                "server": {
                    "v2": [
                        null
                    ],
                    "netconf": 830
                }
            }
        },
        "Cisco-IOS-XR-ifmgr-cfg:interface-configurations": {
            "interface-configuration": [
                {
                    "active": "act",
                    "interface-name": "GigabitEthernet0/0/0/5",
                    "shutdown": [
                        null
                    ]
                },
                {
                    "active": "act",
                    "interface-name": "GigabitEthernet0/0/0/4",
                    "shutdown": [
                        null
                    ]
                },
                {
                    "active": "act",
                    "interface-name": "GigabitEthernet0/0/0/3",
                    "shutdown": [
                        null
                    ]
                },
                {
                    "active": "act",
                    "interface-name": "GigabitEthernet0/0/0/2",
                    "shutdown": [
                        null
                    ]
                },
                {
                    "active": "act",
                    "interface-name": "GigabitEthernet0/0/0/1",
                    "shutdown": [
                        null
                    ]
                },
                {
                    "active": "act",
                    "interface-name": "GigabitEthernet0/0/0/0",
                    "description": "testing interface"
                },
                {
                    "active": "act",
                    "interface-name": "MgmtEth0/0/CPU0/0",
                    "Cisco-IOS-XR-ipv4-io-cfg:ipv4-network": {
                        "addresses": {
                            "primary": {
                                "address": "192.168.1.214",
                                "netmask": "255.255.255.0"
                            }
                        }
                    }
                }
            ]
        },
        "Cisco-IOS-XR-man-netconf-cfg:netconf-yang": {
            "agent": {
                "ssh": {
                    "enable": [
                        null
                    ]
                }
            }
        }
    }
   }

Check if an interface exists on a device
########################################

It is possible to check if an interface is available on a device by checking if it is available on the operational database.

To check if the interface Loopback123 is available on device R1 run:

.. code-block:: guess

   curl -X GET \
  http://localhost:8181/restconf/operational/network-topology:network-topology/topology/UniConfig/node/R1/frinx-UniConfig-topology:configuration/Cisco-IOS-XR-ifmgr-cfg:interface-configurations/interface-configuration/act/Loopback123 \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache'

If the interface exists the response is:

.. code-block:: guess

   {
    "interface-configuration": [
        {
            "active": "act",
            "interface-name": "Loopback123",
            "description": "description from UniConfig native",
            "interface-virtual": [
                null
            ]
        }
    ]
   }

**NOTE:** If it doesn't exist, a 404 http error will be returned.

Sync configuration from network
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This request allows to synchronize the information from the device and update the UniConfig database.

To sync configuration from device R1 states as follows:

.. code-block:: guess

   curl -X POST \
  http://localhost:8181/restconf/operations/uniconfig-manager:sync-from-network \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Cache-Control: no-cache' \
  -d '{
	"input": {
				"target-nodes": {
					 "node": ["R1"]
		}

	}
}'

To sync all the devices run the same command leaving the target-nodes field empty.

The response of a successful sync is:

.. code-block:: guess

   {
    "output": {
        "node-sync-status-results": {
            "node-sync-status-result": [
                {
                    "nodeId": "R1"
                }
            ]
        }
    }
   }

Add configuration to the config datastore
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This request allows to add a configuration in the config datastore.

To add an interface "Loopback123" on the device R1, run:

.. code-block:: guess

   curl -X PUT \
  http://localhost:8181/restconf/config/network-topology:network-topology/topology/uniconfig/node/R1/frinx-uniconfig-topology:configuration/Cisco-IOS-XR-ifmgr-cfg:interface-configurations/interface-configuration/act/Loopback123 \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache' \
  -d '{
    "interface-configuration": [
        {
            "active": "act",
            "interface-name": "Loopback123",
            "description": "description from UniConfig native",
            "Cisco-IOS-XR-ipv4-io-cfg:ipv4-network": {
                "addresses": {
                    "primary": {
                        "address": "1.2.3.4",
                        "netmask": "255.255.255.128",
                        "route-tag": 22
                    }
                }
            },
            "interface-virtual": [
                null
            ]
        }
    ]
   }'


In case of successful addition of configuration you will be presented with **status 201**.

Commit configuration to the device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This request allows to commit the configurations stored in the config database to the devices.

To commit configurations on device R1 run:

.. code-block:: guess

   curl -X POST \
  http://localhost:8181/restconf/operations/uniconfig-manager:commit \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache' \
  -d '{
            "input": {
                "target-nodes": {
                    "node": ["R1"]
                }
            }
        }'

To commit on all the mounted devices just leave empty the "target-nodes" field.

A successful response will look like this:

.. code-block:: guess

   {
    "output": {
        "node-sync-status-results": {
            "node-sync-status-result": [
                {
                    "nodeId": "R1"
                }
            ]
        }
    }
   }

Calculate diff
~~~~~~~~~~~~~~

This request allows to show the difference between the config datastore and the operational datastore

To calculate the diff run:

.. code-block:: guess

   curl -X POST \
  http://localhost:8181/restconf/operations/uniconfig-manager:calculate-diff \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 26ce61b0-d785-4a3d-bf39-938e1e9a6931' \
  -H 'cache-control: no-cache' \
  -d '{
            "input": {
                "target-nodes": {
                    "node": ["R1"]
                }
            }
           }'

To calculate diff on all the mounted devices just leave empty the "target-nodes" field.
The output will show the differences between config and operational.

**NOTE:** It will be empty in case there are no differences.

Replace config with operational
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

It is also possible to directly replace the content of the config datastore with the content of the operational datastore.

To replace config with operational for the node R1, run:

.. code-block:: guess

   curl -X POST \
  http://localhost:8181/restconf/operations/UniConfig-manager:replace-config-with-operational \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 95be83e1-3203-410e-b707-9eec6325b6ca' \
  -H 'cache-control: no-cache' \
  -d '{
            "input": {
                "target-nodes": {
                    "node": ["R1"]
                }
            }
           }'

To replace the config of all the mounted devices just leave the "target-nodes" field empty.

A successful response will look like this:

.. code-block:: guess

   {
    "output": {
        "result": "complete"
    }
   }

Snapshot
~~~~~~~~

With UniConfig native it is possible to use the snapshot feature. You can create snapshot in order to rollback the config datastore. This can be useful in case we modify the configuration and later we discover that the changes were not working as we expected. By creating a snapshot before the changes it is possible to rollback to a previous configuration version. 

Create a snapshot
+++++++++++++++++

To create a snapshot named "snapshot1" run:

.. code-block:: guess

   curl -X POST \
  http://localhost:8181/restconf/operations/snapshot-manager:create-snapshot \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache' \
  -d '{
    "input": {
        "name": "snapshot1",
        "target-nodes": {
            "node": ["R1"]
        }
    }
}'

To create a snapshot of all the mounted devices just leave the "target-nodes" field empty.

A successful response will look like this:

.. code-block:: guess

   {
    "output": {
        "result": "complete"
    }
}

Replace config with snapshot
++++++++++++++++++++++++++++

Once a snapshot has been created, can be used to replace the config datastore.
To replace the node R1 with "snapshot1" run:

.. code-block:: guess

   curl -X POST \
  http://localhost:8181/restconf/operations/snapshot-manager:replace-config-with-snapshot \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 3065b477-8c97-4385-b160-110df89d8616' \
  -H 'cache-control: no-cache' \
  -d '{
            "input": {
                "name": "snapshot1",
                "target-nodes": {
                    "node": ["R1"]
                }
            }
        }'

To replace the config of all the mounted devices just leave the "target-nodes" field empty.

A successful response will look like this:

.. code-block:: guess

   {
    "output": {
        "result": "complete"
    }
}

In case of failure an error message will be provided:

.. code-block:: guess

   {
    "output": {
        "error-message": "Snapshot with name snapshot1 does not exist.",
        "result": "fail"
    }
}

Delete a snapshot
+++++++++++++++++

To delete the snapshot named "snapshot1" instead run:

.. code-block:: guess

   curl -X POST \
  http://localhost:8181/restconf/operations/snapshot-manager:delete-snapshot \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache' \
  -d '{
    "input": {
        "name": "snapshot1"
    }
}'

A successful response will look like this:

.. code-block:: guess

   {
    "output": {
        "result": "complete"
    }
}

Unmount device
~~~~~~~~~~~~~~

To unmount device R1 run:

.. code-block:: guess

   curl -X DELETE \
  http://localhost:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/R1 \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Postman-Token: c90ca034-f0ab-40b2-b386-564496773d74' \
  -H 'cache-control: no-cache'

In case of success a **Status 200** will be returned. Otherwise a 404 http error code will be returned.

Request 3.1.3 can be used to check if the device has been properly unmounted.

In this case the return code must be 404 since the device does not exisit in UniConfig anymore.

Usage for Junos devices
-----------------------

Enable whitelist 
~~~~~~~~~~~~~~~~

For junos device is necessary just to add a whitelist and it is not needed to blacklist

To set the whitelist run:

.. code-block:: guess

   curl -X PUT \
  http://localhost:8181/restconf/config/direct-unit-matcher:direct-unit-matchers/direct-unit-matcher/junos \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Cache-Control: no-cache' \
  -d '{
 
    "direct-unit-matcher": [
        {
            "name": "junos",
            "capability-regex-matcher": [".*juniper.*", ".*ietf.*"]
        }
    ]
    
}'

Mount JunOS device
~~~~~~~~~~~~~~~~~~

This is the request to mount Junos device:

.. code-block:: guess

   curl -X PUT \
  http://localhost:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/junos \
  -H 'Accept: */*' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Cache-Control: no-cache' \
  -H 'Content-Type: application/json' \
  -d '{
  "node": [
    {
      "node-id": "junos",
      "netconf-node-topology:host": "10.10.199.47",
      "netconf-node-topology:port": 830,
      "netconf-node-topology:keepalive-delay": 50000,
      "netconf-node-topology:tcp-only": false,
      "netconf-node-topology:username": "USERNAME",
      "netconf-node-topology:password": "PASSWORD"
    }
  ]
}'
 
 
Caption:
**junos** is the name of the node
**10.10.199.47** is the ip address of the node
**830** is the port number of the node
**USERNAME** is the username to access the node
**PASSWORD** it is the respective password


Show config
~~~~~~~~~~~

To show all the configurations loaded in config database, run:

.. code-block:: guess

   curl -X GET \
  http://localhost:8181/restconf/config/network-topology:network-topology/topology/uniconfig/node/junos \
  -H 'Accept: */*' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Cache-Control: no-cache' \
  -H 'Content-Type: application/json' \

In case of success it will respond something similar to:

.. code-block:: guess

   {
    "node": [
        {
            "node-id": "junos",
            "frinx-uniconfig-topology:configuration": {
                "configuration:configuration": {
                    "interfaces": {
                        "interface": [
                            {
                                "name": "fxp0",
                                "unit": [
                                    {
                                        "name": "0",
                                        "family": {
                                            "inet": {
                                                "dhcp": {
                                                    "vendor-id": "Juniper-vmx"
                                                }
                                            }
                                        }
                                    }
                                ]
                            },
                            {
                                "name": "ge-0/0/2",
                                "disable": [
                                    null
                                ]
                            },
                            {
                                "name": "ge-0/0/3",
                                "disable": [
                                    null
                                ]
                            },
                            {
                                "name": "ge-0/0/0",
                                "disable": [
                                    null
                                ]
                            },
                            {
                                "name": "ge-0/0/1",
                                "disable": [
                                    null
                                ]
                            }
                        ]
                    },
                    "system": {
                        "processes": {
                            "dhcp-service": {
                                "traceoptions": {
                                    "file": {
                                        "filename": "dhcp_logfile",
                                        "size": "10m"
                                    },
                                    "flag": [
                                        {
                                            "name": "all"
                                        }
                                    ],
                                    "level": "all"
                                }
                            }
                        },
                        "root-authentication": {
                            "encrypted-password": "$6$3Trqy2rm$GYYW/jtCOQIh3wKOHeQS5gaarlvFwkPlhO33ChPtrjdkdjesepwiXgLX2iP81w6RO1hRBV.ZziZlA3oiq8SSW1"
                        },
                        "login": {
                            "class": [
                                {
                                    "name": "admin",
                                    "idle-timeout": 10,
                                    "permissions": [
                                        "all"
                                    ]
                                }
                            ],
                            "user": [
                                {
                                    "name": "test-user",
                                    "uid": 400,
                                    "class": "admin",
                                    "authentication": {
                                        "encrypted-password": "$6$aTUy5Jek$6vfWrHFPakTtGBOy9ZBeq0j3hfSq2D16TuD6Sc38SWbN4nkSlLp.LZa4ulGi7wjhzL1ruXBmvzAyGrpD8Tyrh."
                                    }
                                }
                            ]
                        },
                        "services": {
                            "ssh": {
                                "root-login": "allow"
                            },
                            "netconf": {
                                "ssh": {},
                                "rfc-compliant": [
                                    null
                                ],
                                "yang-compliant": [
                                    null
                                ]
                            }
                        },
                        "syslog": {
                            "user": [
                                {
                                    "name": "*",
                                    "contents": [
                                        {
                                            "name": "any",
                                            "emergency": [
                                                null
                                            ]
                                        }
                                    ]
                                }
                            ],
                            "file": [
                                {
                                    "name": "messages",
                                    "contents": [
                                        {
                                            "name": "any",
                                            "notice": [
                                                null
                                            ]
                                        },
                                        {
                                            "name": "authorization",
                                            "info": [
                                                null
                                            ]
                                        }
                                    ]
                                },
                                {
                                    "name": "interactive-commands",
                                    "contents": [
                                        {
                                            "name": "interactive-commands",
                                            "any": [
                                                null
                                            ]
                                        }
                                    ]
                                }
                            ]
                        },
                        "authentication-order": [
                            "radius"
                        ]
                    },
                    "version": "17.3R1.10"
                }
            }
        }
    ]
}

Show interface configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To show the configuration related to a specific interface, in this case “ge-0/0/2”, run:

.. code-block:: guess

   curl -X GET \
  http://localhost:8181/restconf/config/network-topology:network-topology/topology/uniconfig/node/junos/frinx-uniconfig-topology:configuration/configuration:configuration/interfaces/interface/ge-0%2F0%2F2 \
  -H 'Accept: */*' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Cache-Control: no-cache' \
  -H 'Content-Type: application/json' \

The response will show the status of the interface:

.. code-block:: guess

   {
    "interface": [
        {
            "name": "ge-0/0/2",
            "disable": [
                null
            ]
        }
    ]
}

Enable interface in configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To enable the interface “ge-0/0/2” in config database, run:

.. code-block:: guess

   curl -X PUT \
  http://localhost:8181/restconf/config/network-topology:network-topology/topology/uniconfig/node/junos/frinx-uniconfig-topology:configuration/configuration:configuration/interfaces/interface/ge-0%2F0%2F2 \
  -H 'Accept: */*' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Cache-Control: no-cache' \
  -H 'Content-Type: application/json' \
  -d '{
    "interface": [
        {
            "name": "ge-0/0/2"
        }
    ]
}'

Disable interface in configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To disable the interface “ge-0/0/2” in config database, run:

.. code-block:: guess

   curl -X PUT \
  http://localhost:8181/restconf/config/network-topology:network-topology/topology/uniconfig/node/junos/frinx-uniconfig-topology:configuration/configuration:configuration/interfaces/interface/ge-0%2F0%2F2 \
  -H 'Accept: */*' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Cache-Control: no-cache' \
  -H 'Content-Type: application/json' \
  -d '{
    "interface": [
        {
            "name": "ge-0/0/2",
            "disable": [
                null
            ]
        }
    ]
}'

After the configuration changes have been done on the config database, it is possible to send to the junos device with the commit request. 


