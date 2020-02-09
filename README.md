# SEMbms
Battery Management System for the SEM project.

## Overview
SEMbms is a Battery Management System (BMS) designed for a 6-cell lithium battery.
The cells are expected to be in series, giving a combined nominal voltage of 22.2V.
The battery has a discharge connector for the load, and a balance connector that powers the BMS.
The BMS routes the discharge connector through a latching relay and a fuse.

The fuse is a rated for 50A, and the rest of the components in series with the load should handle 50A for a short period of time
(this needs to be formally tested).

In case of an out-of-bounds connection, it will open the relay to disconnect the load from the battery.
The BMS will monitor the following parameters:
 - Cell voltages
 - Load current
 - Temperature
 - Battery presence

The BMS will continuously check for out-of-bounds conditions in any of these parameters. Specifically:
 - Cell undervoltage (default >3.0V)
 - Cell overvoltage (default <4.3V)
 - High load current (default >35A)
 - High battery temperature (default >60&deg;C, ensures temperature sensor is connected)
 - No battery discharge voltage (battery disconnected)

If any of these conditions occur continuously for a timeout, then the BMS will cut the connection to the load.

## Usage
### Software
The BMS is based on an ATmega3208-MFR/4808-MFR.
It uses a custom PCB design that isn't publicly available at the moment, but hopefully will be in the future.
This makes it difficult to test without the proper hardware, so uses by people outside the SEM project are probably limited.

The code compiles using Atmel Studio 7, and so far we've been using an Atmel-ICE to load the program onto the microcontroller.

#### How to Download
Make sure Git is installed. Run:\
`git clone --recurse-submodules https://github.com/TeddyHut/SEMbms` to get a copy.

If you have already run `git clone` but without the `--recurse-submodules` option, run:\
`git submodule update --init --recursive` to checkout the submodules.

### User Interaction
There is a small directional pad with 5 buttons arranged in a crosshair shape. This gives up, down, left, right, and centre input options.
There is a 2-digit 7-segment display, where each digit has a demical point, for information.

#### Quick Start
Ensure the temperature sensor is connected, the latching relay is in the **OFF** position,
and that the battery is charged with the discharge connector plugged in.

Plug in the balance connector and wait for the BMS to count down from 5 until it closes the relay and is now in *Armed mode*
(indicated by the blinking right decimal point). If any out-of-bounds condition occurs,
the BMS will open the relay and display the cause of error. Hold any button to leave the error-display screen.
Now select **Ar** to go back into *Armed mode*. While in *Armed mode*, press the centre button to open the relay.

To turn off the BMS, pull out the balance connector. The relay will automatically go to the **OFF** position.
