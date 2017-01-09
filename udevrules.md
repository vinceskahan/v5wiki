A single installation of weeWX can be used to collect data from multiple devices on one computer.  But how can you tell which device is which?

If the devices are connected via serial ports, then the serial port identifies them.  For example, `/dev/ttyS0` and `/dev/ttyS1' or 'COM1' and 'COM2'.

If the devices use USB, things get a bit more complicated.

# Example: two USB-to-serial converters

For example, if you have FTDI 2 USB-serial converters, call them `A` and `B`, they will show up as `/dev/ttyUSB0` and `/dev/ttyUSB1`.  But when you unplug `A` then plug it back in, it might show up as `/dev/ttyUSB2`.  How do you ensure that the weeWX instance for device `A` only every talks to device `A`?

Use udev rules to ensure a unique, consistent names for each device.

In the case of the 2 TFDI converters, start with the weeWX configuration for each device.  In the weeWX configuration file for device `A`, use `port = /dev/deviceA`.  Similarly, for device `B` use `port = /dev/deviceB`.

Create a udev rule file `/etc/udev/weewx.rules` that looks like this:

```
# FTDI usb-serial converter for device A
ACTION=="add", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{serial}="XXXXXXXX", SYLINK+="deviceA"
# FTDI usb-serial converter for device B
ACTION=="add", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", ATTRS{serial}="YYYYYYYY", SYLINK+="deviceB"
```
where `XXXXXXXX` is the serial number for the USB-to-serial converter connected to device `A` and `YYYYYYYY` is the serial number for the USB-to-serial converter connected to device `B`.

Find the serial numbers by looking at the detailed USB attributes of each device:

`sudo lsusb -v`

Tell the operating system to reload the rules with this command:

`sudo udevadm control --reload-rules`

Finally, unplug then re-plug the USB cable to each device to trigger udev to make the links.

You will now see `/dev/deviceA` and `/dev/deviceB`, each of which is a symlink to the actual device.  If you unplug and replug devices, weeWX will detect the missing device, retry its connection, then every should continue as normal.  No need to restart weeWX or make changes to configuration files.

# How to distinguish USB devices?

Most software uses VendorID and ProductID to distinguish devices, but this 

# How to determine the uniqueness of each device

The `lsusb -v` command will enumerate the attributes of each USB device.  It is usually a good idea to run it with administrative privileges, e.g., `sudo lsusb -v`, since not all of the attributes are visible without administrative privileges.

To see how those attributes are seen by udev, use the udevadm commands.  For example:

`sudo udevadm info -a -n /dev/ttyUSB0`

will list all of the attributes of the device `/dev/ttyUSB0`, using the labels that udev will recognize.

You must use the udev labels in the rules file; udev will not recognize many of the labels that you see in lsusb output, even though they refer to the same thing.
