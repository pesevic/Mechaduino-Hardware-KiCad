![3D Render of Mechaduino PCB](images\Mechaduino-Hardware-KiCad.png)
# Overview
This repository contains an updated KiCad port of [jcchurch13's](https://github.com/jcchurch13/Mechaduino-Hardware) Mechaduino hardware. 

As a part of my master's thesis, I wanted to revive the Mechaduino-based [TorqueTuner](https://github.com/IDMIL/TorqueTuner), a rotary haptic device developed at the Input Devices and Music Interaction Laboratory (IDMIL) at McGill University. Since the Mechaduino has been out of production for some years, and because the original design files are in EAGLE (a now-defunct, proprietary software), I have ported the schematics and board layout to KiCad, a free and open-source (and incredibly powerful) PCB design software. 

While retaining the original circuit design, I have updated the schematics and created a new PCB layout from scratch, using KiCad's built-in component libraries. I have also included PCB fabrication/assembly files (.gbr, .drl, BOM, CPL). Most importantly, since the design is now more readily accessible, I hope it will be much easier to adapt and redesign the hardware for various stepper motors and applications.

Below, I have included some useful information if you wish to adopt or adapt the Mechaduino for your own project.

# Guide
This section serves as a guide for manufacturing and programming the Mechaduino.
## Mechaduino Summary
The [Mechaduino](https://github.com/jcchurch13/Mechaduino-Hardware) is an open-source stepper motor servo designed by [Tropical Labs](https://tropical-labs.com/mechaduino/). Its relatively small size and affordability make it attractive for mechatronics applications.

Integrated circuits on the board include a motor driver (A4954), magnetic encoder (AS5047D), 3.3 V linear voltage regulator (TLV70233), and an Arduino Zero-compatible microcontroller (ATSAMD21G18A-A). It can be powered via USB or an external 5 V source. The motor is powered through a terminal block, accepting a supply voltage between 8 V and 35 V. A range of Arduino I/O pins are accessible through the header pins, including those for I2C and SPI communication. See the schematic for more detail.

The board is designed to fit onto the back of a NEMA 17 motor.

## PCB Manufacturing
If you wish to obtain Mechaduino PCBs for yourself, you may follow what I suggest below, which is how I ordered them for myself.

For manufacturing and assembly, I used [jlcpcb.com](jlcpcb.com). Upload the zipped fabrication files to JLC, and add the PCB assembly option. Since two-layer boards can only be assembled on one side, choose the top side. The magnetic encoder and its decoupling capacitor can be soldered manually later. Upload the corresponding bill of materials and component position list .csv files. **Note that you may need to select alternative parts or reposition components in the board preview.** I have had to flip C12 and align J9 (USB connector) each time I've processed the PCBs.

## Device Assembly
Besides the Mechaduino PCB, you will need the following parts (the links direct to the parts I used):
 - [a NEMA 17 stepper motor](https://www.omc-stepperonline.com/nema-17-bipolar-45ncm-64oz-in-2a-42x42x40mm-4-wires-w-1m-cable-connector-17hs16-2004s1),
 - [a diametrically magnetized NdFeB magnet](https://www.digikey.ca/en/products/detail/radial-magnets-inc/8996/5126078),
 - an AS5074D magnetic encoder IC,
 - an 0.1 uF 0805 SMD capacitor,
 - 2.54 mm pitch header pins,
 - long M3 screws/threaded rods,
 - standoffs of some sort (I used washers), and
 - super glue.

Follow these steps (based on instructions in [Tropical Labs' manual](https://tropical-labs.com/wp-content/uploads/2018/11/Mechaduino-Manual-0.1.3.pdf)):
1. Solder the encoder and capacitor to the back of the PCB with an iron/reflow/oven. Also add the header pins.
2. Super glue the magnet onto the back of the motor shaft. Center it as much as possible.
3. Mount the PCB to the motor using the threaded rods and standoffs. The PCB should sit a couple of milimeters away from the motor.

Your Mechaduino should now be ready to be programmed.

## Programming
Flashing an Arduino Zero bootloader onto the custom MCU circuit was, for me, the most challenging part of this build. I used an ST-LINK V2 USB programmer paired with [Adafruit Adalink](https://github.com/adafruit/Adafruit_Adalink) to upload the bootloader via the PCB's SWD pins. This may be easier on Mac, but I can only speak for the Herculean procedure required for Windows users. Besides the USB programmer, you will need some way of connecting the programmer to the pins. I used an SWD breakout board connected to the programmer with jumper wires.

Follow the installation instructions on the [Adalink repository](https://github.com/adafruit/Adafruit_Adalink). Note, however, that I could not get Adalink to work with any version of Python newer than 2.7.13 (this was using Adalink 2.2.0 and OpenOCD 0.12.0). You may need to downgrade Python to an older version.

Once you have confirmed that Adalink is working in CMD, clone the [ArduinoCore-samd repository](https://github.com/arduino/arduinocore-samd) to get the bootloader. CD into `/ArduinoCore-samd/bootloaders/zero`. Wipe the board and flash the bootloader with the following two commands:
```
adalink atsamd21g18 -p stlink -w
adalink -v atsamd21g18 -p stlink -h samd21_sam_ba.hex
```
Check whether the 5888 bytes are verified. If not, unplugging and plugging the ST-LINK USB dongle into my computer seemed to fix things. Once the MCU has successfully been flashed, connect the Mechaduino to your computer via the micro-USB port. Double-pressing the reset button on the PCB should make the device appear as an Arduino Zero in Device Manager or in the Arduino IDE.

Finally, clone the [Mechaduino Firmware](https://github.com/jcchurch13/Mechaduino-Firmware) repository and upload the code to the board via the Arduino IDE. You should now have a fully functioning Mechaduino. Run the calibration routine and tests as described in [Tropical Labs' manual](https://tropical-labs.com/wp-content/uploads/2018/11/Mechaduino-Manual-0.1.3.pdf).


# License
This project is released under the [Creative Commons Attribution-ShareAlike 4.0](https://creativecommons.org/licenses/by-sa/4.0/) license, and is not endorsed by the original creator.

*No generative AI was used in any part of this project.*