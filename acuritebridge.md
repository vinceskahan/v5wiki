# How to get data from an Acurite bridge

The Acurite bridge listens for radio signals from Acurite sensors such as the 5-in-1, Tower, and Temperature/Humidity.  Then it transmits sensor data via a wired connection to a TCP/IP network.

When it was first introduced in 2013, the bridge sent data to the web service 'acu-link.com'.

In July 2016, Acurite introduced the 'myacurite.com' web service and new firmware to make the bridge device work with it.  The transition to new firmware took place from September to December 2016.  The new firmware sends data to 'myacurite.com' and optionally to the weather underground.

The bridge has a small web server on port 80, but it displays only its status and configuration, as well a an option to turn the LEDs on or off.  It does not display sensor readings.

The bridge can detect many different types of Acurite sensors, such as the 5-in-1, the Tower, and various types of Temperature/Humidity sensors.

The bridge appears to send data for every sensor that it detects.  However, the myacurite web server supports only up to 10 devices.

## Strategies

There are a two approaches to getting data from an Acurite bridge: sniff or listen.

### Sniff

In the sniff approach, a computer monitors the local network for traffic from the Acurite bridge.  Packets from the bridge are decoded then fed into weeWX.  The sniffing must be done on a device that can see the network traffic from the Acurite bridge.  For example, it can happen on the network router, a computer connected to the same hub or port-mirroring switch as the Acurite bridge, or on a device that bridges the Acurite bridge to the rest of the network.

### Listen

In the listen approach, the bridge is fooled into sending data to a computer that appears to be myacurite.com.  Data sent by the bridge are decoded then fed into weeWX.

This is typically implemented by creating a local DNS entry that maps hubapi.myacurite.com to a the network address of a local computer.  Then weeWX runs on the local computer, receiving and decoding the data sent by the bridge.  In a slight variation of this approach, a web server on the local computer runs a PHP script to receive the data, then the PHP script transfers the data to weeWX, typically by an intermediate file.

## Credits

Rich of Modern Toil did some of the first packet sniffing and decoding:

    http://moderntoil.com/?p=794

George Nincehelser figured out some crucial parts of the encoding and fed it into weewx:

    http://nincehelser.com/ipwx/

Perhaps the first weewx driver was a sniffer called haculink:

    http://geekfun.com/hackulink/

The interceptor driver implements both the sniff and listen approaches for the acurite bridge:

    http://github.com/matthewwall/weewx-interceptor

Now that packet sniffing is much more common, and thanks to the lack of any magic numbers, the protocol used by the July 2016 firmware was decoded within weeks by many different people.