# Industrial Shields PLC Raspberry Pi on balena 

This is an example on how to run the Industrial Shields PLC Raspberry Pi 4 on balenaOS.

## Description

[Industrial Shields PLC Raspberry Pi](https://www.industrialshields.com/es_ES/industrial-plc-pac-raspberry-pi-202009) is an industrial single board computer that guarantees adaptability to all types of sensors, data, and communication options. The key features include improved processing speed, an uninterrumped power supply, real-time clock, a wide range of connections such as dual Ethernet ports, dual RS-485, WiFi, Bluetooth and more, including up to 36 Digital Inputs, up to 16 Analog Inputs, up to 22 Digital Outputs, up to 8 Analog Outputs, up to 23 Relays and up to 6 Interrupt Inputs.

In this example project we use the [balena platform](https://balena.io) to deploy the required libraries to use all the connections and IO capabilities of the Industrial Shields PLC Raspberry Pi. Find also deployed Node-RED along the Industrial Shields libraries to quickstart with your first industrial prototypes with Node-RED.

The balena platform enables provisioning, updating the software and OS, and managing large fleets of industrial devices from balenaCloud. 

## Requirements

### Hardware

* the [Industrial Shields PLC Raspberry Pi 4 v4 Model 21](https://www.industrialshields.com/es_ES/shop/raspberry-plc-21-2230?attrib=73-416&view_mode=grid#attr=2607,702,743,4073,534,7119,7120,143,5807,4218,4219,4220)
* the [Industrial Shields Power Supply](https://www.industrialshields.com/es_ES/shop/is-ac12vdc2-5adin-fuente-alimentacion-carril-din-30w-12v-salida-659?attrib=49-456&view_mode=grid#attr=3657)
* a Modbus Temperature and Humidity sensor 

### Software

* a [balena account](https://dashboard.balena-cloud.com/signup) - free for up to 10 devices
* [balenaEtcher](https://etcher.balena.io/)
* and a SD card


## Deploy

You have two options here:

### One-click deploy via balena Deploy

Use the button below to create your fleet (of one or more devices) and deploy the code.

[![](https://balena.io/deploy.svg)](https://dashboard.balena-cloud.com/deploy?repoUrl=https://github.com/Industrial-Shields/industrialshields-balena)

If you don't have a balenaCloud account, no worries, you will create it after clicking the `Deploy with balena` button.


### Deploy via balena CLI

Or, you can create an application in your balenaCloud dashboard and balena push this code to it the traditional way installing the [balena CLI](https://www.balena.io/docs/reference/balena-cli/).

- Sign up on [balena.io](https://dashboard.balena.io/signup)
- Create a new fleet on balenaCloud.

Now open a Terminal on your computer:

```
cd ~/workspace
git clone https://github.com/mpous/industrialshields-balena
cd industrialshields-balena
```

- Using [Balena CLI](https://www.balena.io/docs/reference/cli/), push the code with `balena push <your-fleet-name>`
- See the magic happening, your device is getting updated 🌟Over-The-Air🌟!


## Add a device

Once the release is successfully built on your fleet in balenaCloud, click `Add device`.

And select the device type `Raspberry Pi 4 (using 64bit OS)` balenaOS version. Add the Wi-Fi credentials if needed, and click `Flash` or `Download` the balenaOS image.

![Flash balenaOS](https://github.com/Industrial-Shields/industrialshields-balena/assets/173156/651ee471-9104-4307-9ceb-094a36a70113)

Use balenaEtcher to flash the SD card with the balenaOS image.

### Add the custom dtbo of Industrial Shields

Download the Industrial Shields Raspberry Pi PLC `dtbo` overlay file to add to the OS image. [Click here to download the latest dtbo file](https://apps.industrialshields.com/main/rpi/rpiplc_click_v4/sc16is752-spi1-rpiplc-v4.dtbo).

Once flashed, connect the SD to your computer to add an extra `dtbo` file into the `overlays` folder of the balenaOS. The unit is called `resin-boot`.

![Industrial Shields dtbo](https://github.com/mpous/industrialshields-balena/assets/173156/734b42c7-2879-47c9-be24-0ea2e3571f44)

Once the file is properly saved on the SD card, connect the SD card into the Rasberry Pi 4 PLC and power it up.

### Configure the DT Overlay

Define the DT Overlays to enable all the I/O capabilities of the PLC.

* Edit the `Define DT overlays` field and introduce these device tree overlays: `"vc4-kms-v3d","spi0-2cs,cs0_pin=7,cs1_pin=8","w5500,cs=0,int_pin=6,speed=10000000","i2c-rtc,ds3231","sc16is752-spi1-rpiplc-v4,xtal=14745600"`.

* Edit the `Define DT parameters for the default overlay` with: `"spi=on","i2c_arm=on"`.

* Edit the `Allows GPIO pins to be set to specific modes and values at boot time.` and add these parameters: `"8=pd","16=pu"`.

![balenaCloud Define DT Overlays](https://github.com/mpous/industrialshields-balena/assets/173156/3c6f40f8-09fc-41ab-9e3b-d26d76278b52)

If you are running a fleet of devices, you should do this from the Fleet level in the Fleet `Configuration` section. Then all the devices from your fleet will inherit the DT overlays and parameters.


## Run the application

In this case, we are deploying Node-RED along the necessary libraries to run the Industrial Shields libaries and drivers.

### Node-RED

For running Node-RED, use the local IP address on port 80, if you are on the same network than your device. You also can use the `Publick Device URL` by balena to access to the Node-RED UI.

#### Device Variables

In case they do not exist, add these variables at balenaCloud at device level using `Device Variables` or at a Fleet level at `Variables` for all the devices.

Variable Name | Default | Description
------------ | ------------- | -------------
PORT | `80` | the port that exposes the Node-RED UI
USERNAME | `balena` | the Node-RED admin username
PASSWORD | `balena` | the Node-RED admin password
ENCRIPTION_KEY | `balena` | the encription key used to store your credentials files

You **must** set the `USERNAME` and `PASSWORD` variables to be able to access, save or run programs in Node-RED.  

![balenaCloud Device Variables](https://github.com/mpous/industrialshields-balena/assets/173156/f219e049-1efc-4b5f-baf4-79c4a5f21c13)

#### Add the rpiplc node

Once you access to the Node-RED UI, go to the top-right menu and click `Manage pallette` and then go to the `Install` tab to find and install the Industrial Shields PLC node.

![Industrial Shields librpiplc](https://github.com/mpous/industrialshields-balena/assets/173156/3b79f2c0-3f87-45d5-b2e9-5cb249bc34c8)

Search `librpiplc` and you might find the industrial shields bindings for Node-RED. Click install.


## Attribution

This is in joint effort between Ricard and Eloi from Industrial Shields PLC and Marc Pous from balena.io.

If you have any quesions about the project feel free to use the [Industrial Shields forums](https://www.industrialshields.com/forum/forum-controllers-plc-17) or the [balena forums](https://forums.balena.io).

## Disclaimer

This project is for educational purposes only. Do not deploy it into your production systems without understanding what you are doing. 

