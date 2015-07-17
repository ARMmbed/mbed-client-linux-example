# Getting started on LWM2M Client Example

This document describes briefly the steps required to start using the LWM2M Client example application on Linux platform. The LWM2M Client example application demonstrates how to register, unregister, read resource values and send resource observations to the mbed Device Server.

## Required hardware

* 64 bit Ubuntu/XUbuntu OS desktop environment 
**Note** This example is targetted for linux platform and this is only for building,compiling and running on Linux platform. 

## Required software

* [yotta](http://docs.yottabuild.org/#installing) - to build the example programs.
* [mbed Device Server (mDS)](#download-mbed-device-server-mds) version 2.2 or later - the LWM2M Client example connects to mDS.

## Optional software
* [Wireshark](https://www.wireshark.org/) - for packet inspection/network debugging.

## Setting up the environment

To set up the environment, you will need to do the following:

1. Download and run mDS server on computer.
2. Download and run mbed Connected Home on computer.
3. Configure the LWM2M Client example program with the server address.
4. Build the application with Yotta.
5. Run the application from the command prompt.

**Note:** You might need to open UDP port 5683 in your computer firewall for mDS to communicate with this example application.
**Note:** In case you are running Ubuntu on virtual machine, you should change your computer's Connection Settings from `NAT Firewall` to `Bridged` else you might notice that the incoming UDP packets starts dropping after few minutes. 

### Downloading mbed Device Server (mDS)

The example application will register to mbed Device Server. Install mDS on your local computer.

1. The free developer version of the mbed Device Server is used with this example. Download it from [ARM silver](https://silver.arm.com/browse/SEN00).
2. Unzip the package on your local computer. You should see the following files:
NOTE: These instructions have been made for Device Server 2.4 release!
```
Device Server.tar.gz
Device Server Clients.tar.gz
Device Server Tools.tar.gz
Ref Apps.tar.gz
```

### Starting the mbed Device Server (mDS)

1. Go to the `bin` folder of the Device Server package that you downloaded.
2. Run the start script:
    - If you are running mDS on Linux OS, run the `runDS.sh` in a new shell.
    - If you are running mDS on Windows, run the `runDS.bat` in a new command prompt.
		
This will start the mbed Device Server on your system.
		
### Starting the WebUI ("Connected home" reference app)

1. Go to the `bin` folder in the Connected-home-trial reference app that you downloaded.
2. Run the start script:	
    - If you are running mDS on Linux OS, run the `runConnectedHome.sh` in a new shell.	
    - If you are running mDS on Windows, run the `runConnectedHome.bat` in a new command prompt.	
		
This will start the WebUI on your system.	
		
## mbed Build instructions		
		
### Building
You can use this example in any of the following three connection modes:

1. Non-secure mode
2. DTLS PSK mode
3. DTLS Certificate mode

The general instructions for all modes are the same. The only difference comes in step 5, when selecting the mode.

#### General 
1. Install yotta. See instructions [here](http://docs.yottabuild.org/#installing).
2. Install the necessary toolchains. Refer to the yotta installation instructions (in step 1) to learn how to install the toolchains.
3. In the command prompt, `cd` **examples** and **lwm2m-client-example**.
4. Open file `source/main.cpp`, edit your mbed Device Server's Ipv4 address in place of `coap://<xxx.xxx.xxx.xxx>`. For example, if your server's IP address is `192.168.0.1`, you would enter `coap://192.168.0.1`.
5. Select connection mode from below
6. Set up the target device, `yotta target x86-linux-native`.
7. In the command prompt type `yotta build`. The executable file will be created to `build/x86-linux-native/source/` folder.

**Note:** If the Device Server does not respond to _Client hello_ in secure connection modes, check your settings and restart the Device Server and the WebUI.

#### Non-secure
1. Set the `CONN_MODE` value to `M2MSecurity::NoSecurity`.
2. Set `MBED_SERVER_PORT` to `5683`.

#### DTLS PSK
1. Set the `CONN_MODE` value to `M2MSecurity::Psk`.
2. Set `MBED_SERVER_PORT` to `5684`.

#### DTLS Certificate
1. Set the `CONN_MODE` value to `M2MSecurity::Certificate`.
2. Set `MBED_SERVER_PORT` to `5684`.
3. Close the WebUI and Device Server and go to `<Device server>/conf/ `.
4. In the `deviceserver.properties` file, set `coap.dtls.psk` to `false`.
5. Restart the Device Server and WebUI.
6. In the browser, navigate to `https://localhost:8081/security.html` and give **admin** both as the username and the passsword.
7. Open the **Security** tab and upload `source/cert.pem` to the **Trusted certificates** list.

### Running the LWM2M Client example

1. Find the executable file named `lwm2m-client-linux-example` in the folder `lwm2m-client-linux-example/build/x86-linux-native/source/`.
2. Run the executable from command line using `./lwm2m-client-linux-example`
3. The program begins execution and will start registration to the mbed Device Server giving console output as `Registering endpoint` and when the registration is successful it will display `Registered`.
4. After a successful registration, the program will automatically start sending observations after every 10 seconds and it will be visible on console as `Sending observation` along with the value which increments everytime the obeservation is sent like `Value sent 1` and so on. These values can be observed on mbed Connected Home under **/Test/0/D** resource.

## Testing

### Logging network traffic (optional)

1. Start Wireshark on the computer where the mbed Device Server is running.
2. Select your ethernet interface, usually **Local Area Connection**.
3. Click **Start**.
4. Select the **Filter** field in the toolbar and add a filter to correspond to your mbed Device Server. Press Enter after you have entered the expression to activate the filter.
  - For example, if your server's IP address is `123.123.123.123`, you would enter `ip.addr == 123.123.123.123` and press Enter.
5. Run your example application from command line.

**Note** If you are running mDS on same host as the example application then you have to select loopback interface in wireshark.

You should see the endpoint once it has registered with the mbed Device Server.

### Testing the LWM2M Client example application with the mbed Device Server

Ensure that the mDS and the mbed Connected Home are running (see [Setting up the environment](#setting-up-the-environment)). Also, ensure that the example application is running on your linux desktop (see [Running the lwm2m client example](#running-the-lwm2m-client-example)).

**Step 1**: To open the mbed Connected Home, navigate to `http://localhost:8082`.
    - If you are working from a remote machine, you need to use the host machine's IP address instead of "localhost".

**Step 2**: Enter `demo` as both the username and password.

**Step 3**: Go to the **End-points** tab. After a short time your device should appear in the list (refresh the page to update the list).

![Node registered](img/registered.jpg)

**Step 4**: Click the endpoint name to view the registered resources. 

![Resource list](img/endpoint_resources.jpg)

**Step 5**: To make a CoAP request to endpoints resource, click **Read**.

![Read resources](img/read_resources.jpg)

The **/Test** tab contains resources demonstrating dynamic and static resource functionality. 

The **/Test/0/D** represents the dynamic resource observed by the mbed Device Server. It is linked with the example application as it is automatically sending observations after every 10 seconds (once the registration is done). The values are updated to the UI without a need to press **Read**.

![Dynamic Resource](img/dynamic_resource.jpg)

The **/Test/0/S** represents the static resource that is a fixed value set in the mbed Client. To make a CoAP request to the node resources, click **Read**. This returns the fixed value of "Static value".

![Static Resource](img/static_resource.jpg)

To stop and unregister the LWM2M Client example, you need to interrupt the program by selecting CTRL+C, for which the console will display `Unregistering endpoint`.This will send an unregister message to mbed Device Server. After a successful unregistration, the console will display `Unregistration done --> exiting` and it will terminate the program. Also, the endpoint will disappear from the endpoint list of the mbed Connected Home.
