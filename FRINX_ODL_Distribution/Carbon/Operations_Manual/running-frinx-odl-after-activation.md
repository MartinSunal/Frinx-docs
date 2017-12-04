[Documentation main page](https://frinxio.github.io/Frinx-docs/)
[Operations Manual main page](https://frinxio.github.io/Frinx-docs/FRINX_ODL_Distribution/Carbon/operations_manual.html)
<!-- TOC START min:1 max:3 link:true update:true -->
- [Running the FRINX ODL distribution after initial activation](#running-the-frinx-odl-distribution-after-initial-activation)
  - [Operating karaf in 'regular' mode (in the foreground, with console)](#operating-karaf-in-regular-mode-in-the-foreground-with-console)
    - [Starting karaf](#starting-karaf)
    - [Stopping karaf](#stopping-karaf)
  - [Operating karaf in the background](#operating-karaf-in-the-background)
    - [Starting karaf](#starting-karaf-1)
    - [Confirming karaf is running](#confirming-karaf-is-running)
    - [Connecting to the background process](#connecting-to-the-background-process)
    - [Stopping karaf](#stopping-karaf-1)
  - [Operating karaf in 'server' mode (in the foreground, without the console)](#operating-karaf-in-server-mode-in-the-foreground-without-the-console)
    - [Starting karaf](#starting-karaf-2)
    - [Confirming karaf is running](#confirming-karaf-is-running-1)
    - [Stopping karaf](#stopping-karaf-2)
  - [Resetting FRINX ODL to a clean state](#resetting-frinx-odl-to-a-clean-state)
  - [Setting JAVA_HOME and other variables](#setting-java_home-and-other-variables)

<!-- TOC END -->

# Running the FRINX ODL distribution after initial activation

After running for the first time and generating a local license file, you no longer need to provide a token when starting karaf.
The following operations have been tested in CentOS and Ubuntu 16.04.

*Note that Frinx ODL needs approximately 3 minutes to startup and shutdown. To maintain system integrity, **please do not interrupt the startup and shutdown processes** within this time.*  
*In the event of interruption, the initial state can be restored by entering the following commands from a terminal within your Frinx ODL main directory. The first command forcibly kills the Frinx ODL karaf process; the second command cleans certain directories:*
 
```
kill -9 $(pgrep  -o -f  karaf)
rm  -rf  data/ /snapshots/ journal/
```

## Operating in 'regular' mode (karaf in the foreground, with console)
### Starting
In your Frinx ODL Distribution directory, for example /home/username/distribution-karaf-3.1.0.frinx, type

    ./bin/karaf

### Stopping
To stop from within the karaf console there are three options:

1. Hold the 'CTRL' key and press the 'd' key
2. Type:
```
    halt
```
3. Type:
```
    shutdown
```
When prompted to confirm, type 'yes'.

## Operating in the background
### Starting 
In your Frinx ODL Distribution directory, for example /home/username/distribution-karaf-3.1.0.frinx, type
```
    ./bin/start
```
This starts the Frinx ODL karaf process.
### Confirming karaf is running
Type
```
    ./bin/status
```
### Connecting to the background process
Type
```
    ./bin/client
```
By default, client tries to connect on localhost, on port 8101 (the default Apache Karaf SSH port). Client accepts different options to let you connect on a remote Apache Karaf instance. You can use --help to get details about these options.

or type
```
    ssh karaf@localhost -p 8101
```
(password: karaf)

(This connection can be local or remote.)

When connected to the background process, you can logout (this closes only the ./bin/client process, but not the Frinx ODL server) by typing
```
    logout
```
When connected to the background process, you can shutdown the Frinx ODL server by typing  
```
    shutdown
```

### Stopping 
Type
```
    ./bin/stop
```

## Operating in 'server' mode (karaf in the foreground, without the console)
### Starting 
In your Frinx ODL Distribution directory, for example /home/username/distribution-karaf-3.1.0.frinx, type
```
    ./bin/karaf server &
```

### Confirming karaf is running
Type
```
    ./bin/status
```

### Stopping the background process
With karaf running in the background (from using ./bin/start), stop it from within a terminal by typing
```
    ./bin/stop
```

## Resetting FRINX ODL to a clean state
To 'reset' your distribution to a clean state and delete any features previously installed type the following within your frinx ODL distribution directory (e.g. /home/username/distribution-karaf-3.1.0.frinx)
```
    rm -rf data/ cache/ journal/ snapshots/
```

## Setting JAVA_HOME and other variables
This is done by editing the 'setenv' file in the bin directory within your Frinx ODL Distribution directory. Uncomment the relevant line and set the variable as required e.g. to set the location of your Java home directory, uncomment the JAVA_HOME variable and point it to the appropriate folder depending on your Java installation:
```
    export JAVA_HOME=/opt/jdk1.8.0_151
```

