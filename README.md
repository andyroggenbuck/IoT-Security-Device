# IoT Security Device Project
<p align="center">
<img src="https://github.com/andyroggenbuck/IoT-Security-Device/blob/main/images/Finished%20Device.jpg" height="350"><br/>
<i>Finished IoT security device</i></p>


I designed, prototyped, and programmed two USB devices as part of my senior group project at Ferris State University. The project is a cloud-connected security camera system featuring USB hardware modules that can be interchanged to customize the functionality of the unit.  

The devices I designed are an Alarm Module and a Weather Sensor Module -- both are USB High Speed CDC devices that use serial data to communicate with the host (a Raspberry Pi). They are both built using an Adafruit Trinket M0, which is programmed in C using Microchip's MPLAB X IDE and Harmony V3 framework.

## Alarm Module  
<p align="center">
<img src="https://github.com/andyroggenbuck/IoT-Security-Device/blob/main/images/Alarm%20Module%20Top%20Side.jpg" height="300"> <img src="https://github.com/andyroggenbuck/IoT-Security-Device/blob/main/images/Alarm%20Module%20Underside.jpg" height="300"><br/>
<i>This device plays an alarm sound when commanded by the host, using a built in USB-powered amplifier and speaker.</i></p>  

## Weather Sensor Module  
<p align="center">
<img src="https://github.com/andyroggenbuck/IoT-Security-Device/blob/main/images/Weather%20Module%20Top%20Side.jpg" height="300"> <img src="https://github.com/andyroggenbuck/IoT-Security-Device/blob/main/images/Weather%20Module%20Underside.jpg" height="300"><br/>
<i>This device reads pressure, temperature, and humidity values from a sensor via I2C and reports them to the host at regular intervals.</i></p>  

## Developing USB Device Application Code
I could see that writing my own USB driver code would be daunting, and I wasn't confident I'd complete it by the end of the semester, so I started looking for existing drivers I could use with the SAM D microcontroller. I found a helpful starting point for developing the project in [this](https://microchipdeveloper.com/harmony3:usb-getting-started-training-module) example project from Microchip, which uses the USB driver stack in the MPLAB Harmony V3 framework.

I needed to adapt the code to run on the Trinket M0 instead of the evaluation kit used in the example. This required me to make two changes in the Harmony 3 Configurator:

- Assign the USB data pins to be used by the USB peripheral.
  - The USB device wasn't enumerating successfully when plugged into the host, and the problem was simply that the microcontroller's USB data pins were not configured to function as such.
  - This seems obvious in retrospect, but it wasn't mentioned in Microchip's project tutorial and it took me a minute to realize the pins weren't assigned already.

- Change the Bulk IN endpoint address in the CDC Function Driver from 3 to 2.
  - Once enumerated, the device would schedule a USB read and then get hung up waiting for it to complete. I traced the program's execution using breakpoints and found that the endpoint address was being rejected as invalid when passed to a function in the USB driver.
  - Datasheets confirmed the USB endpoints are numbered differently in the Trinket M0's ATSAMD21E18A than they are in the ATSAME70Q21 used in the example project I was working from.

These two fixes established successful USB serial communication, which I used as the foundation for developing the applications used in the [Alarm Module](https://github.com/andyroggenbuck/USB-Alarm-Module) and [Weather Sensor Module](https://github.com/andyroggenbuck/USB-Weather-Sensor-Module).

## Programming the Trinket M0
The Trinket M0 ships with a bootloader installed and is intended to be easily programmed via its USB connector using CircuitPython or Arduino, but I needed to program it using the MPLAB X IDE to utilize the Harmony V3 framework. This required access to the Serial Wire Debug interface of the Trinket's Atmel SAM D microcontroller, which is obtained by soldering leads to two small pads on the Trinket. This allowed me to use an MPLAB Snap programmer/debugger to erase the bootloader and program the Trinket using MPLAB X.

<p align="center">
<img src="https://github.com/andyroggenbuck/IoT-Security-Device/blob/main/images/Trinket%20SWD%20Access.jpg" height="300"> <img src="https://github.com/andyroggenbuck/IoT-Security-Device/blob/main/images/Trinket%20Programming%20Setup.jpg" height="300"><br/>
<i>Access to SWCLK and SWDIO pins via solder pads allows SWD programming using MPLAB Snap programmer/debugger.</i></p><br/>

## [Alarm Module Code Repository and More Details Here](https://github.com/andyroggenbuck/USB-Alarm-Module)<br/><br/>
## [Weather Sensor Module Code Repository and More Details Here](https://github.com/andyroggenbuck/USB-Weather-Sensor-Module)
