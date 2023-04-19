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

### Configure the raspberry pi

There are many guides available for bootstrapping a raspberry pi.  There are also many guides available for installing and configuring a real-time clock in the raspberry pi.  Do it!

The rest of this guide requires only command-line access to the pi.  So you can do the steps below remotely logged in via ssh, or in a terminal window with a keyboard and monitor plugged in to the pi.

```
# install operating system on sdcard, use it to boot the rpi

# install a real-time clock and enable it

# configure the rpi for remote access and headless operation

# get rid of fake clock
sudo apt-get remove --purge fake-hwclock

# ensure the correct timezone
sudo dpkg-reconfigure tzdata
```

### Install weeWX

When you install weeWX, select `Simulator` when prompted for the station type.  You will change it later to `SDR` when you run the `wee_config --reconfigure` command.
```
# install weeWX
wget -qO - http://weewx.com/keys.html | sudo apt-key add -
wget -qO - http://weewx.com/apt/weewx.list | sudo tee /etc/apt/sources.list.d/weewx.list
sudo apt-get update
sudo apt-get install weewx

# shut down weeWX
sudo /etc/init.d/weewx stop

# install weewx-maxbotix extension and enable the driver
sudo wee_extension --install https://github.com/matthewwall/weewx-maxbotix
sudo wee_config --reconfigure
```

### Configure

The maxbotix driver requires a sensor type and communication port.
