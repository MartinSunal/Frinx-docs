# SBE Installation: Optional advanced configuration

<!-- TOC START min:1 max:3 link:true update:true -->
- [SBE Installation: Optional advanced configuration](#sbe-installation-optional-advanced-configuration)
    - [Setting up Multiple SBE Instances](#setting-up-multiple-sbe-instances)
    - [Generating new SSH keys](#generating-new-ssh-keys)
    - [Using Apache Directory Studio](#using-apache-directory-studio)
    - [Importing LDIF template into Apache Directory](#importing-ldif-template-into-apache-directory)

<!-- TOC END -->

The following are optional steps and not required for a standard installation.

### Setting up Multiple SBE Instances

You can provide more than one instance on more than one host. By default SBE has one instance pre-configured, named "default". All the configuration files for this are located in the "sbe/instances/default" directory.

**Instance configuration description**

*   You can see all available instances, using command "./sbe --help" and focus to text Available instances.
*   All instance specific configuration files are concetrated in "instances/default" directory which is root directory of instance
*   New instance you can create by copy folder "default" e.g. as "my_instance" and change configuration files inside this dir

**Instance directory structure**

    instances/  
             └── default - Instance name and root directory  
             ├── config - Instance configuration file  
             ├── config.images - Optional image configuration file  
             ├── .keys - Folder for SSH keypair (generated by OpenSSH)  
                 └── ldap  
                     └── base.ldif - LDAP ldif files


**Instance configuration file**

HOST_NAME="localhost" -> **Host or FQDN where is instance provided**  
HOST_PORT=80 -> **Port where the instance listens**

RUN_APACHEDS=true -> **List here the components you wish to start in the instance**  
RUN_REDMINE=true  
RUN_POSTGRESQL=true  
RUN_GERRIT=true  
RUN_JENKINS=true  
RUN_NEXUS=true  
RUN_SONARQUBE=true

VOLUME_DRIVER="local" -> **Specify volume driver**

VOLUME_HOSTPATH_APACHEDS_DATA="" -> **Optional: Define volume host-path**  
VOLUME_HOSTPATH_NEXUS_DATA="" in existing volume  
VOLUME_HOSTPATH_GERRIT_DATA=""

NET_SUBNET="172.28.0.0/24" -> **Change subnetwork and IP for each component**  
NET_IP_GATEWAY="172.28.0.1" when you provide more than one instance on host  
NET_IP_GERRIT="172.28.0.2" NET_IP_NEXUS="172.28.0.3"

### Generating new SSH keys

By default the installation adds an ssh-hey for you. However, if you wish to re-generate a key for security reasons, run the following command from a terminal:

    ssh-keygen  


You will receive the following response:

        Generating public/private rsa key pair.  
    Enter file in which to save the key (/home/<username>/.ssh/id_rsa): Confirm the default path to your SBE instance:


Enter the full pathname to the id_rsa file. This should be /home/<username>/sbe/instances/default/.keys/id_rsa (replace username accordingly).

Enter a passphrase (recommended) or leave it blank. Remember this passphrase, as you will need it to unlock the key whenever you use it.

Note that id_rsa.pub is your public key and can be shared, while id_rsa is your private key and should be kept secret.

### Using Apache Directory Studio

If you prefer to use a GUI tool to add/delete and modify your directory contents you can use Apache Directory Studio. You can install this tool on your host if you have an OS version with a GUI available. You will also need Java installed. Alternatively, you can install the tool on any machine that has IP reachability to the container running the OpenLDAP server.

Apache Directory Studio is free for Linux, Mac & Windows. [Download: Apache Directory Studio][1] Apache Directory Studio is a complete directory tooling platform intended to be used with any LDAP server however it is particularly designed for use with ApacheDS. [More about: Apache Directory Studio][2]

### Importing LDIF template into Apache Directory

Open Apache Directory Studio. Create a connection to the Docker container named "apache-directory". Host: check the IP address in the config.sbe file. Port: change to 10389. Go to the LDAP browser -> Right click on "Root DSE", select "Import" and then "LDIF Import" from the pull-down menu. Select the LDIF file from "ldap/base.ldif" and click the Import button.

 [1]: http://directory.apache.org/studio/downloads.html
 [2]: http://directory.apache.org/