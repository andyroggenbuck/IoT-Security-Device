# IoT Security Device Project
I designed, prototyped, and programmed the electronics for two USB devices as part of my senior group project at Ferris State University. The project is a cloud-connected security system with USB hardware modules that can be interchanged to customize the functionality of the unit. These modules include a camera, an alarm, and a weather sensor.

The devices I designed are the Alarm Module and Weather Sensor Module -- both are USB High Speed CDC devices that use serial data to communicate with the host (a Raspberry Pi). They are both built using an Adafruit Trinket M0, which is programmed in C using Microchip's MPLAB X IDE and Harmony V3 framework.
<br/>  

## Alarm Module - [Details and Code Repository Here](https://github.com/andyroggenbuck/USB-Alarm-Module)  
<p align="center">
<img src="https://github.com/andyroggenbuck/IoT-Security-Device/blob/main/images/Alarm%20Module%20Top%20Side.jpg" height="300"> <img src="https://github.com/andyroggenbuck/IoT-Security-Device/blob/main/images/Alarm%20Module%20Underside.jpg" height="300"><br/>
<i>This device plays an alarm sound when commanded by the host, using a built in USB-powered amplifier and speaker.</i></p>
<br/>  

## Weather Sensor Module - [Details and Code Repository Here](https://github.com/andyroggenbuck/USB-Weather-Sensor-Module)  
<p align="center">
<img src="https://github.com/andyroggenbuck/IoT-Security-Device/blob/main/images/Weather%20Module%20Top%20Side.jpg" height="300"> <img src="https://github.com/andyroggenbuck/IoT-Security-Device/blob/main/images/Weather%20Module%20Underside.jpg" height="300"><br/>
<i>This device reads pressure, temperature, and humidity values from a sensor via I2C and reports them to the host at regular intervals.</i></p>
<br/>  

## Programming the Trinket M0
The Trinket M0 ships with a bootloader installed and is intended to be easily programmed via its USB connector using CircuitPython or Arduino, but I struggled to find USB drivers I could use with these platforms that provided flexibility about which USB device classes could be used.

I found a more flexible and powerful USB driver stack in the MPLAB Harmony V3 Framework. To use it I needed to program the Trinket using the MPLAB X IDE, which required access to the Serial Wire Debug interface of the Trinket's Atmel SAM D microcontroller, which is obtained by soldering leads to two small pads on the Trinket. This allowed me to use an MPLAB Snap programmer/debugger to erase the bootloader and program the Trinket using MPLAB X.

<p align="center">
<img src="https://github.com/andyroggenbuck/IoT-Security-Device/blob/main/images/Trinket%20SWD%20Access.jpg" height="300"> <img src="https://github.com/andyroggenbuck/IoT-Security-Device/blob/main/images/Trinket%20Programming%20Setup.jpg" height="300"><br/>
<i>Access to SWCLK and SWDIO pins via solder pads allows SWD programming using MPLAB Snap programmer/debugger.</i></p><br/><br/>

## USB Device Application Code
I found a helpful starting point for developing the USB device application code in [this](https://microchipdeveloper.com/harmony3:usb-getting-started-training-module) example project from Microchip.

The code in this project has more complexity and hardware abstraction than I had dealt with before. I needed to adapt the code to run on the Trinket M0 instead of the evaluation kit used in the example, and it took me a while to find what I needed to change, even though it turned out to be just two small tweaks made in the Harmony 3 Configurator:

- Assign the USB DM and DP pins to be used by the USB peripheral. This seems obvious in retrospect, but it wasn't mentioned in the project tutorial and it took me a while to realize they weren't assigned already.

- Change the Bulk IN endpoint number in the CDC Function Driver from 3 to 2. The USB endpoints are numbered differently in the Trinket M0's ATSAMD21E18A than they are in the ATSAME70Q21 used in the example.

This established successful USB serial communication, which I used as the foundation for developing the [Alarm Module](https://github.com/andyroggenbuck/USB-Alarm-Module) and [Weather Sensor Module](https://github.com/andyroggenbuck/USB-Weather-Sensor-Module).
