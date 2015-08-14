# Getting started on LWM2M Client Example

This document describes briefly the steps required to start using the LWM2M Client example application on Linux platform. The LWM2M Client example application demonstrates how to register, read resource values and send resource observations to the mbed Device Server (mDS), and deregister from it.

## Required hardware

* 64 bit Ubuntu/XUbuntu OS desktop environment 
**Note:** This example is targetted for building, compiling, and running on Linux platform only. 

## Required software

* [yotta](http://docs.yottabuild.org/#installing) - to build the example programs.
* [mbed Device Server (mDS)](#download-mbed-device-server-mds) version 2.2 or later - the LWM2M Client example connects to mDS.

## Optional software
* [Wireshark](https://www.wireshark.org/) - for packet inspection and network debugging.

## Setting up the environment

To set up the environment, you will need to do the following:

1. Download and run the mDS on your computer.
2. Download and run mbed Connected Home on your computer.
3. Configure the LWM2M Client example program with the server address.
4. Build the application with yotta.
5. Run the application from the command prompt.

**Note:** You may need to open UDP port 5683 in your computer firewall for mDS to communicate with this example application.
In case you are running Ubuntu on virtual machine, you should change your computer's Connection Settings from `NAT Firewall` to `Bridged`. Otherwise, incoming UDP packets may start dropping after a few minutes. 

### Downloading mDS

Install mDS on your local computer. The example application will register with the mDS. 

1. The free developer version of the mDS is used with this example. Download it from [ARM silver](https://silver.arm.com/browse/SEN00).
2. Unzip the package on your local computer. You should see the following files:

```
Device Server.tar.gz
Device Server Clients.tar.gz
Device Server Tools.tar.gz
Ref Apps.tar.gz
```
**Note:** These instructions are valid for mDS 2.4 release.

### Starting the mDS

1. Go to the `bin` folder of the Device Server package that you downloaded.
2. Run the start script:
    - If you are running mDS on Linux OS, run the `runDS.sh` in a new shell.
    - If you are running mDS on Windows, run the `runDS.bat` in a new command prompt.
		
This will start the mDS on your system.
		
### Starting the WebUI (_Connected home_ reference app)

1. Go to the `bin` folder in the _Connected-home-trial_ reference app that you downloaded.
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
4. Open file `source/main.cpp`, edit the Ipv4 address of mDS in place of `coap://<xxx.xxx.xxx.xxx>`. For example, if your server's IP address is `192.168.0.1`, you would enter `coap://192.168.0.1`.
5. Select the connection mode as instructed below.
6. Set up the target device, `yotta target x86-linux-native`.
7. In the command prompt, type `yotta build`. The executable file will be created to `build/x86-linux-native/source/` folder.

**Note:** If mDS does not respond to _Client hello_ in secure connection modes, check your settings and restart mDS and the WebUI.

#### Non-secure
1. Set the `CONN_MODE` value to `M2MSecurity::NoSecurity`.
2. Set `MBED_SERVER_PORT` to `5683`.

#### DTLS PSK
1. Set the `CONN_MODE` value to `M2MSecurity::Psk`.
2. Set `MBED_SERVER_PORT` to `5684`.

#### DTLS Certificate
1. Set the `CONN_MODE` value to `M2MSecurity::Certificate`.
2. Set `MBED_SERVER_PORT` to `5684`.
3. Close the WebUI and mDS and go to `<Device server>/conf/ `.
4. In the `deviceserver.properties` file, set `coap.dtls.psk` to `false`.
5. Restart mDS and WebUI.
6. In the browser, navigate to `https://localhost:8081/security.html` and give **admin** both as the username and the passsword.
7. Open the **Security** tab and upload `source/cert.pem` to the **Trusted certificates** list.

### Running the LWM2M Client example

1. Find the executable file named `lwm2m-client-linux-example` in the folder `lwm2m-client-linux-example/build/x86-linux-native/source/`.
2. Run the executable from command line using `./lwm2m-client-linux-example`
3. The program begins execution and starts registration with mDS. The console displays **Registering endpoint** and when the registration is successful it displays **Registered**.
4. After a successful registration, the program will automatically start sending observations every 10 seconds, displaying **Sending observation** on the console. The console also displays a value that increments everytime the observation is sent. For example, **Value sent 1**. These values are displayed in mbed Connected Home application under the **/Test/0/D** resource.

## Testing

### Logging network traffic (optional)

1. Start Wireshark on the computer where the mDS is running.
2. Select your Ethernet interface, usually **Local Area Connection**.
3. Click **Start**.
4. Select **Filter** in the toolbar and add a filter to correspond to your mDS. Activate the filter and press Enter.
  - For example, if your server's IP address is `123.123.123.123`, you would enter `ip.addr == 123.123.123.123` and press Enter.
5. Run your example application from the command line.

**Note:** If you are running mDS on the same host as the example application you have to select the _loopback interface_ in Wireshark.

You will see the endpoint when it has registered with the mDS.

### Testing the LWM2M Client example application with the mDS

Make sure that the mDS and the mbed Connected Home application are running (see [Setting up the environment](#setting-up-the-environment)). Also, make sure that the example application is running on your Linux desktop (see [Running the lwm2m client example](#running-the-lwm2m-client-example)).

**Step 1**: To open the mbed Connected Home application, navigate to `http://localhost:8082`.
    - If you are working from a remote machine, you need to use the host machine's IP address instead of _localhost_.

**Step 2**: Enter `demo` as both the username and password.

**Step 3**: Go to the **End-points** tab. Your device should appear in the list in a moment (refresh the page to update the list).

![Node registered](img/registered.jpg)

**Step 4**: Click the endpoint name to view the registered resources. 

![Resource list](img/endpoint_resources.jpg)

**Step 5**: To make a CoAP request to the endpoints resource, click **Read**.

![Read resources](img/read_resources.jpg)

The **/Test** tab contains resources demonstrating dynamic and static resource functionality. 

**/Test/0/D** represents the dynamic resource observed by the mDS. It is linked with the example application as it is automatically sending observations after every 10 seconds (when the registration is done). You do not need to press **Read** to update the values.

![Dynamic Resource](img/dynamic_resource.jpg)

**/Test/0/S** represents the static resource that is a fixed value set in the mbed Client. To make a CoAP request to the node resources, click **Read**. This returns the fixed value of _Static value_.

![Static Resource](img/static_resource.jpg)

To stop and deregister the LWM2M Client example, you need to interrupt the program by pressing CTRL+C. The console displays **Unregistering endpoint**. This will send an unregister message to mDS. After a successful deregistration, the console displays **Unregistration done --> exiting** and terminates the program. Also, the endpoint disappears from the endpoint list of the mbed Connected Home application.
