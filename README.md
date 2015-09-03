# Getting started on Mbed Client Linux Example

**DISCLAIMER**: This example application connects to [ARM mbed Device Connector](https://connector.mbed.com), a new web service hosted by ARM. However, at the time of publishing this example, the service may not yet be live or fully ready for use. If the example doesn't work for you, or you are having problems with it, that probably means we haven't yet got the service online. Please look out for mbed Device Connector release announcements in [ARM mbed](https://mbed.com). If, however, you are mbed partner and have your own mbed Device Server setup, you can use this example application just by changing the mbed Device Server address from `api.connector.mbed.com` to your own mbed Device Server address. 

This document describes briefly the steps required to start using the mbed Client example application on Linux platform. The mbed Client example application demonstrates how to register, read resource values  to mbed Device Connector and deregister from it.

## Required hardware

* 64 bit Ubuntu/XUbuntu OS desktop environment

**Note:** This example is targetted for building, compiling, and running on Linux platform only. 

## Required software

* [yotta](http://docs.yottabuild.org/#installing) - to build the example programs.

## Optional software
* [Wireshark](https://www.wireshark.org/) - for packet inspection and network debugging.

## Setting up the environment

To set up the environment, you will need to do the following:

1. Go to [mbed Device Connector website](http://connector-test.dev.mbed.com) and log in with your mbed.org account.
2. Configure the mbed Client example program with desired parameters. See [mbed Build instructions](#mbed-Build-instructions) chapter for more information.
4. Build the application with yotta.
5. Run the application from the command prompt.

**Note:** You may need to open UDP port 5683/5684 in your computer firewall for mbed Device Connector to communicate with this example application.
In case you are running Ubuntu on virtual machine, you should change your computer's Connection Settings from `NAT Firewall` to `Bridged`. Otherwise, incoming UDP packets may start dropping after a few minutes. 

## mbed Build instructions		
		
### Building
You can use this example in following connection modes:

1. Non-secure mode
2. Certificate mode

The general instructions for both modes are the same. The only difference comes in step 5, when selecting the mode.

#### General 
1. Install yotta. See instructions [here](http://docs.yottabuild.org/#installing).
2. Install the necessary toolchains. Refer to the yotta installation instructions (in step 1) to learn how to install the toolchains.
3. In the command prompt, go to **mbed-client-linux-example**.
4. Select and set the connection mode as instructed below.
5. Set up the target device, `yotta target x86-linux-native`.
6. In the command prompt, type `yotta build`. The executable file will be created to `build/x86-linux-native/source/` folder.

#### Setting Non-secure mode
1. Set the `CONN_MODE` value to `M2MSecurity::NoSecurity`.
2. Set `MBED_SERVER_PORT` to `5683`.
3. Open `sources/security.h` with your code editor to set the registration domain. You **must** use your **mbed developer account username** as a domain name. Enter your **mbed developer account username** as domain in `MBED_DOMAIN`.
4. The endpoint registration name is defined as `MBED_ENDPOINT_NAME` in `sources/security.h`. You can change it by modifying it with your code editor.

#### Setting Certificate mode
1. Set the `CONN_MODE` value to `M2MSecurity::Certificate`.
2. Set `MBED_SERVER_PORT` to `5684`.
3. Go to  [mbed Device Connector website](http://connector-test.dev.mbed.com).
4. Navigate to **Security credentials** under **My devices**.
5. Click **GET MY DEVICE SECURITY CREDENTIALS**. You will get the needed certificate information as well as the endpoint name and domain.
6. Copy the created security credentials to `sources/security.h`

### Running Mbed Client Linux example

1. Find the executable file named `mbed-client-linux-example` in the folder `mbed-client-linux-example/build/x86-linux-native/source/`.
2. Run the executable from command line using `./mbed-client-linux-example`
3. The program begins execution and starts registration with mbed Device Connector. The console displays **Registering endpoint** and when the registration is successful it displays **Registered**.
4. After a successful registration, the program will automatically start incrementing one of the resource value every 10 seconds, displaying **Resource Value /Test/0/D : <Value>** on the console. For example, **Resource Value /Test/0/D : 1**. These values can be read from the mbed Device Connector through Test API.

## Testing

### Logging network traffic (optional)

1. Start Wireshark on the computer where your example application is running.
2. Select your Ethernet interface, usually **Local Area Connection**.
3. Click **Start**.
4. Select **Filter** in the toolbar and add a filter to correspond to UDP port that you are using (5683 for non-secure and 5684 for secure). Activate the filter and press Enter.
5. Run your example application from the command line.
**Note:** If you are running mDS on the same host as the example application you have to select the _loopback interface_ in Wireshark.


### Testing Mbed Client Linux example application with mbed Device Connector

Ensure that you have the example application running on your linux environment(see [Running Mbed Client Linux example](#running-mbed-client-linux-example)).

**Step 1**: Go to [mbed Device Connector website](http://connector-test.dev.mbed.com).

**Step 2**: Log in using your mbed account.

**Step 3**: Click the **Connected devices** link under **My devices** to see your registered mbed Client example device.

**Step 4**: You can send requests to mbed Client device with mbed Device Connector API. To do that, click **API Console** under **mbed Device Connector**. Click the URL to create a request. For example: `http://ds-test.dev.mbed.com/v1/endpoints/lwm2m-endpoint/Test/0/S` creates a GET request to the static **/Test/0/S** resource.

The **/Test/0/S** represents the static resource that is a fixed value set in the mbed Client. 

The **/Test/0/D** represents the dynamic resource that can be read by the mbed Device Server. It is linked with the program which automatically start incrementing the resource value every 10 seconds, displaying **Resource Value /Test/0/D : <Value>** on the console. The value starts from zero and increments the value by 1. You can make a CoAP request to the node resources to get the latest value. To do that, click **API Console** under **mbed Device Connector**. Click the URL to create a request. For example: `http://ds-test.dev.mbed.com/v1/endpoints/lwm2m-endpoint/Test/0/D` creates a GET request to the **/Test/0/D** resource.This returns the latest value of **/Test/0/D**. 

To stop and deregister the Mbed Client example, you need to interrupt the program by pressing CTRL+C. The console displays **Unregistering endpoint**. This will send an unregister message to mDS. After a successful deregistration, the console displays **Unregistration done --> exiting** and terminates the program. Also, the endpoint disappears from the **Connected devices** list of mbed Device Connector Web UI.
