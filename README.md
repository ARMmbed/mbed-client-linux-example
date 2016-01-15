# Getting Started with mbed Client on Linux

This document describes how to start using the mbed Client example application on Linux. The mbed Client example application demonstrates how to register to mbed Device Connector, read resource values and deregister from it.

**Note:** This example is targeted for building, compiling, and running on the Linux platform only.

## Required software and setup

* 64 bit Ubuntu/XUbuntu OS desktop environment.

* Install yotta and all necessary toolchains. See instructions [here](http://yottadocs.mbed.com/#installing).

* You may need to open UDP port 5683 or 5684 in your computer's firewall for mbed Device Connector to communicate with this example application.

* If you are running Ubuntu on a virtual machine, you should change your computer's Connection Settings from `NAT Firewall` to `Bridged`. Otherwise, incoming UDP packets may start dropping after a few minutes.

## Optional software

* [Wireshark](https://www.wireshark.org/) - for packet inspection and network debugging.

## Quick guide

To run the example:

1. Go to the [mbed Device Connector website](https://connector.mbed.com) and log in with your mbed.org account.

2. Clone this example into your local computer.

3. Configure the mbed Client example program as needed. See the [Running and build instructions](#running-and-build-instructions) chapter for more information.

4. Build the application with yotta. See the [Running and build instructions](#running-and-build-instructions) chapter for more information.

## Running and build instructions

This example works in secure Certificate mode.

### Review: building for two connection modes
#### Setting socket type

You can also connect in different socket mode. To select the binding mode for the socket, change `SOCKET_MODE` between `M2MInterface::UDP` and `M2MInterface::TCP`. The instructions further in this document remain te same irrespective of the socket mode you select.

### Building

First, made sure you have installed yotta and all necessary toolchains. See instructions [here](http://yottadocs.mbed.com/#installing).

1. In the command prompt, go to **mbed-client-linux-example**.

2. Set up the application with correct Certificate and Domain:

	A. Go to the [mbed Device Connector website](https://connector.mbed.com).

	B. Go to **My devices -> Security credentials**.

	C. Click **GET MY DEVICE SECURITY CREDENTIALS**. You will get the needed certificate information as well as the endpoint name and domain.

	D. Copy the security credentials to `sources/security.h`.

3. In the command prompt, type `yotta target x86-linux-native` to set up the target device.

4. In the command prompt, type `yotta build`. The executable file will be created in the folder `build/x86-linux-native/source/`.

### Running the mbed Client Linux example

1. In the folder `mbed-client-linux-example/build/x86-linux-native/source/`, find the executable file `mbed-client-linux-example`.

2. To run the executable from the command line, type `./mbed-client-linux-example`.

3. The program begins execution and starts registration with mbed Device Connector. The console displays **Registering endpoint** and when the registration is successful it displays **Registered**.

4. After a successful registration, the program will automatically start incrementing one of the resource values every 10 seconds, displaying **Resource Value /Test/0/D : <Value>** on the console. For example, **Resource Value /Test/0/D : 1**. You can read the values from the mbed Device Connector through the Test API.

## Testing

### Logging network traffic (optional)

1. Start Wireshark on the computer where your example application is running.

2. Select your Ethernet interface, usually **Local Area Connection**.

3. Click **Start**.

4. Select **Filter** in the toolbar and add a filter to correspond to the UDP port that you are using (5684 for secure). Activate the filter and press Enter.

5. Run your example application from the command line.

**Note:** If you are running mbed DS on the same host as the example application you have to select the _loopback interface_ in Wireshark.

### Testing the mbed Client Linux example application with mbed Device Connector

Ensure that you have the example application running on your Linux environment (see [Running the mbed Client Linux example](#running-the-mbed-client-linux-example)).

1. Go to the [mbed Device Connector website](https://connector.mbed.com).

2. Log in using your mbed account.

3. Go to **My devices -> Connected devices** to see your registered mbed Client example device.

4. You can send a CoAP request to the mbed Client device with the mbed Device Connector API. To do that:

	- Go to **mbed Device Connector -> API Console**.

	- Click the URL to create a request. For example: `https://api.connector.mbed.com/endpoints/<MBED_ENDPOINT_NAME>/Test/0/SS` creates a GET request to the static **/Test/0/SS** resource.
		- **/Test/0/DD** represents the dynamic resource that the mbed DS can read. The example program automatically increments the resource's value every 10 seconds, displaying **Resource Value /Test/0/D : <Value>** on the console. The value starts from zero and the increments are steps of one.
		- **/Test/0/SS** represents the static resource that has a fixed value in the mbed Client.

**NOTE:** If you changed your endpoint's name (the variable `MBED_ENDPOINT_NAME` in `sources/security.h`) from the default `mbed-linux-endpoint`, you will need to change it in the above URLs as well.

For more information on the mbed Device Connector REST API, see help pages.

To stop and deregister the mbed Client example, you need to interrupt the program by pressing CTRL+C. This sends an unregister message to mbed DS. The console displays **Unregistering endpoint**. After a successful deregistration, the console displays **Unregistration done --> exiting** and terminates the program. Also, the endpoint disappears from the **Connected devices** list on the mbed Device Connector web UI.
