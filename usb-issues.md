# USB hardware issues

We often see reports of problems with weather stations that are connected to the computer using USB.  Typically these problems appear more often with raspberry pi (especially older models).  Some weather stations seem to be more prone to USB issues (e.g., older fine offset stations such as 1080/2080/3080), but other stations may have issues as well (te923, acurite usb consoles).

Typical errors include:

```
error sending control message: Broken pipe
```
```
could not detach kernel driver from interface 0: No data available
```

In the case of Fine Offset stations, the USB communication will fail (the `FineOffset lockup`).  Communication with Acurite or TE923 stations may lock up as well.

## USB cables

Use quality USB cables.  If there is noise from radio or electromagnetic sources, use cables with a ferrite core.

## USB power

Be sure that there is sufficient and stable power for the weather station.  Some computers do not provide sufficient power over the USB.  On some systems, the USB communication voltages can be noisy when other devices are powered by or even plugged in to the USB on the computer.

To find out if this is a problem on your system, connect the weather station to a powered (not passive) USB hub, then connect the hub to the computer.

## Minimize devices on the USB

Remove every other USB device from the computer other than the weather station.

Note that this can be tricky with some computers, since some computers use USB for their networking, either with a USB-Ethernet/wifi dongle, or even internally (many single-board computers use USB internally for ethernet, even if they have a proper ethernet connector).

## Try different computer hardware

Some users have reported problems when using a weather station with a raspberry pi, but no problems using the same station on an x64 computer (or even an orangePC).  Try using a different computer as a quick way to see if there is a fundamental hardware problem.

## Try different software versions

Some users have reported problems with one kernel version, only to find that reverting to a previous version (or updating to a new version) makes the problems go away.

There are also cases where a weather station works well with one architecture (e.g., x86 or x64) but does not work another (e.g., mips).  This is typically due to bugs/behavior in specific kernel or hardware driver, not the architecture.  But running the weather station on different platforms can help track down the root cause.

