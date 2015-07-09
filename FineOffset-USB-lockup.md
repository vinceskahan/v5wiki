### What to do when your Fine Offset station locks up

The Fine Offset stations are known to periodically stop communicating via USB.  The weather station console continues to communicate with the sensors, and weather data are displayed properly on the weather station console.  However, any communication over USB stops.  Since the lockup happens in the station firmware, no weather station software is immune.  It happens with weewx, Cumulus, pywws, wview, EasyWeather, etc.

Jim Easterbrook (author of pywws) surmises that the lockup happens when a computer attempts to communicate with the station while the station is reading data from the sensors (every 48 or 60 seconds) or writing to console memory (every archive interval).

It is possible that the lockup is due to hardware configuration.  Some people experience the lockup with certain computers but not others, some report fewer lockups after switching to a powered hub or to a different USB cable.

It would appear to be a problem with stations manufactured after 2010 or so; older stations seem to be immune.  It might coincide with a change in radio communication protocols between console and sensors.

The USB lockups were first noted in 2011.

### What does it look like?

When the lockup happens, you will see a gap in weather data.  In the weewx log you will see messages such as this:
```
Jun  7 21:50:33 localhost weewx[2460]: fousb: get archive interval failed 
attempt 1 of 3: could not detach kernel driver from interface 0: No data available
```
The "could not detach kernel driver from interface ... No data available" is the sign of a lockup.

### How to fix it

The only way to get things working again is to power cycle the station.  That means remove the batteries, disconnect the USB connection, then reinstall the batteries and reconnect the USB.

There is no need to restart weewx.

Since the station receives power from USB, removing the batteries without disconnecting USB will not power cycle the station.

### Workarounds for unattended operation

Obviously, this firmware bug makes the FineOffset stations unsuitable for unattended operation.  However, there are some workarounds:

#### Use correct settings

The best settings for a Fine Offset station using weewx 2.6.x and earlier are:
```
[FineOffsetUSB]
    polling_mode = PERIODIC
    polling_interval = 60
[StdArchive]
    archive_interval = 300
    record_generation = software
```
Also, set the station's archive interval to 5 minutes (300 seconds) using wee_config_fousb:

```
sudo wee_config_fousb --set-interval=5
```

These settings are not a guarantee against lockups, but they will reduce the chance.

With weewx 2.6.x and earlier, using polling_mode = ADAPTIVE or record_generation = hardware will increase the chance of a lockup.

#### Use a switched hub

Connect the station, without batteries, to a USB hub that has per-port switching.  When a lockup is detected, power cycle the station by turning the power off/on in the USB hub.

This feature is included in the fousb.py driver in weewx as of r2225 or weewx 2.6.4.  When weewx detects a lockup, it tells the USB hub to power cycle the station.

To enable this feature, add the following to weewx.conf:
```
[FineOffsetUSB]
    power_cycle_hub = 001:016
    power_cycle_port = 1
```
power_cycle_port indicates the port to which the station is attached.  On a four-port hub this will be 1, 2, 3, or 4. Note that the port number may not be the same as the socket number. This is a known
problem with the DUB-H7. You'll have to experiment to find the right port number.

power_cycle_hub uniquely identifies the hub to which the station is attached.  It has the format XXX:YYY where XXX is the bus and YYY is the device, for example 001:016.  Use lsusb to determine these numbers.  For example, in the following output, the linksys USB2HUB4 is located at 001:016:
```
% lsusb
Bus 001 Device 026: ID 1941:8021 Dream Link WH1080 Weather Station / USB Missile Launcher
Bus 001 Device 021: ID 0557:2008 ATEN International Co., Ltd UC-232A Serial Port [pl2303]
Bus 001 Device 019: ID 1130:6801 Tenx Technology, Inc. 
Bus 001 Device 016: ID 0409:0058 NEC Corp. HighSpeed Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```
Beware that many hubs may say that they support per-port power switching when, in fact, they do not.  This is often because the controller chip supports per-port switching, but the USB hub manufacturer did not hook up the pins that actually supply power.

Use a tool such as usb_control.py to see whether a hub actually works.

http://lancet.mit.edu/mwall/projects/weather/releases/usb_control-0.6.py

For example, this will turn off the indicator light for port 3:

```
sudo ./usb_control.py --hub 001:016 --port 3 --indicator 0
```

This will turn off power for port 3:

```
sudo ./usb_control.py --hub 001:016 --port 3 --power 0
```

Hubs that are known to work:

* LINKSYS USB2HUB4 (NEC Corp. HighSpeed Hub)
* D-Link Corp. DUB-H7 7-port USB 2.0 hub (older grey/silver version)

Hubs that are known to not work:

* rosewill 7-port USB hub (TERMINUS TECHNOLOGY INC.) 
* satechi 12-port USB hub (TERMINUS TECHNOLOGY INC.)
* cerulian 7-port USB hub (Genesys Logic, Inc. USB-2.0 4-Port HUB)
* D-Link DUB-H7BL 7-port USB 2.0 hub (Action Star Enterprise Co., Ltd. new black version)
* D-Link DUB-H4 USB 2.0 Hub (Genesys Logic, Inc. USB 2.0 Hub)

#### Beware of random factory resets

Some consoles randomly reset some or all of their settings to factory defaults.  It seems to happen when the console is power cycled, but it does not happen repeatably, and it does not happen to every console.  When it happens, the station's archive interval will change to 1800 seconds (30 minutes).  You will see log messages such as this:

wxengine: The archive interval in the configuration file (300) does not match the station hardware interval (1800).

Use wee_config_fousb to change the station's archive interval to match whatever you specify in weewx.conf.

### References

Discussion at the Cumulus forum about the effects of power/voltage:

http://sandaysoft.com/forum/viewtopic.php?f=16&t=10510

Description of the problem at the Cumulus forum:

http://sandaysoft.com/forum/viewtopic.php?f=13&t=8870