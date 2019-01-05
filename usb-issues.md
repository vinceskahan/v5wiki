# USB hardware issues

We often see reports of problems with weather stations that are connected to the computer using USB.  Typically these problems appear more often with raspberry pi (especially older models).  Some weather stations seem to be more prone to USB issues (e.g., older fine offset stations such as 1080/2080/3080), but other stations may have issues as well (te923, acurite usb consoles).

## USB cables

Use quality USB cables.  If there is noise from radio or electromagnetic sources, use cables with a ferrite core.

## USB power

Be sure that there is sufficient and stable power for the weather station.  Some computers do not provide sufficient power over the USB.  On some systems, the USB communication voltages can be noisy when other devices are powered by or even plugged in to the USB on the computer.

To find out if this is a problem on your system, connect the weather station to a powered (not passive) USB hub, then connect the hub to the computer.

## Minimize devices on the USB

Remove every other USB device from the computer other than the weather station.

Note that this can be tricky with some computers, since some computers use USB for their networking, either with a USB-Ethernet/wifi dongle, or even internally (many single-board computers use USB internally for ethernet, even if they have a proper ethernet connector).

## 
