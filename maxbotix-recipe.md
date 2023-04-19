# Use Maxbotix ultrasonic sensor to measure water level

This is a guide to use a Maxbotix ultrasonic sensor to measure water level.  The ultrasonic sensors can measure the amount of water in a tank, the tide height, or even snow depth.

Maxbotix makes *many* different models of ultrasonic sensor.  The sensor capabilities vary in the resolution of measurements, the minimum distance between sensor and object, the maximum range, and the beam width.  Other options include the communication protocol and form factor.  There are even options for factory-applied coatings to inhibit corrosion.

https://maxbotix.com/

This guide uses the MB7363 HRXL-MaxSonar-WRLS, which has the following characteristics:
* resolution: 1 mm
* detection: first target
* range: 500-9999 mm
* beam angle: moderate
* read rate: 6 Hz
* communications: RS232

Data collection happens on a computer to which the sensor is attached.  The computer can be a raspberry pi, an intel NUC, or any other device that (1) can run weewx, and (2) has a serial port and/or USB port.

Note that this configuration does not have a data logger; when the computer is off, no data are collected.  So be sure to put the computer on a UPS that has enough battery to get through the longest anticipated power outage.  Even better, put the computer on a battery that is connected to a solar panel or other renewable energy source.

The recipes in this guide assume a Debian-like operating system.  The syntax will be slightly different for other operating systems, but the principles and steps are the same.

## What does it look like?

<img src="maxbotix-recipe/MB7363.png" width="200">
<img src="maxbotix-recipe/tide-sensor.png" width="200">
<img src="maxbotix-recipe/tank-sensor.png" width="200">

## What do you need?

| cost | description | source |
|---|---|---|
| $138 | MB7363 | https://maxbotix.com/products/mb7363 |
| $78 | RPi 3B+ | https://www.amazon.com/ELEMENT-Element14-Raspberry-Pi-Motherboard/dp/B07BDR5PDW/ |

Prices are US$ as of April 2023.

## Recipes

### Install the sensor

Connect the sensor to a serial port on the computer.

<img src="maxbotix-recipe/serial-port-pinout.png" width="200">

If the computer does not have a serial port, then wire up a USB-to-serial adapter.

<img src="maxbotix-recipe/usb-serial-pinout.png" width="200">

The sensor requires 5V power, so get that from one of the USB ports on the computer.

<img src="maxbotix-recipe/sensor-power.png" width="200">

Verify the sensor operation by reading directly from the serial port.  The `screen` tool is perhaps the easiest way to do this.  If it is not already on your system, install it with your system's package manager:
```
sudo apt install screen
```

The first thing you must do is determine to which port the sensor is connected.  If the sensor is connected directly to a serial port, then the port will be `/dev/ttyS0` or `/dev/ttyS1` on most x86_64 systems running linux.  On ARM systems running linux, the serial port will be `/dev/ttyAMA0` or `/dev/ttyS0`, or there might be a symlink `/dev/serial0` or `/dev/serial1`.

Assuming that the sensor is connected to the first serial port, then invoke screen like this:
```
sudo screen /dev/ttyS0
```
The `sudo` is probably necessary, since non-privileged users are typically not allowed to write to the serial ports in a default system configuration.

When `screen` is running, you should see output like this:
```
R1034
```
Place an object in front of the sensor, then move it closer and further from the sensor.  You should see the number change as you move the object.

To quit `screen`, type `ctrl-a k`, then type `y` in response to the prompt.

### Configure the computer

The rest of this guide requires only command-line access to the computer.  So you can do the steps below remotely logged in via ssh, or in a terminal window with a keyboard and monitor on the computer.

```
# install operating system

# if the computer is a raspberry pi, install a real-time clock and enable it

# configure the computer for remote access and headless operation - enable sshd

# get rid of fake clock
sudo apt-get remove --purge fake-hwclock

# configure the network time daemon
sudo apt install ntpd

# ensure the correct timezone
sudo dpkg-reconfigure tzdata
```

### Install weeWX

When you install weeWX, select `Simulator` when prompted for the station type.  You will change it later to `Maxbotix` when you run the `wee_config --reconfigure` command.
```
# install weeWX
wget -qO - http://weewx.com/keys.html | sudo apt-key add -
wget -qO - http://weewx.com/apt/weewx.list | sudo tee /etc/apt/sources.list.d/weewx.list
sudo apt-get update
sudo apt-get install weewx

# shut down weeWX
sudo /etc/init.d/weewx stop

# install weewx-maxbotix extension
sudo wee_extension --install https://github.com/matthewwall/weewx-maxbotix

# configure weewx to use the maxbotix sensor
sudo wee_config --reconfigure
```

### Configure

The maxbotix driver requires a sensor type and communication port.