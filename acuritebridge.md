# How to get data from an Acurite bridge

The Acurite bridge listens for radio signals from Acurite sensors such as the 5-in-1, Tower, and Temperature/Humidity.  Then it transmits sensor data via a wired connection to a TCP/IP network.

When it was first introduced in 2013, the bridge sent data to the web service 'acu-link.com'.

In July 2016, Acurite introduced the 'myacurite.com' web service and new firmware to make the bridge device work with it.  The transition to new firmware took place from September to December 2016.  The new firmware sends data to 'myacurite.com' and optionally to the weather underground.

The bridge is running a small web server on port 80, but it displays only its status and configuration, as well as minimal options to modify that configuration.  It does not display sensor readings.

## Strategies

There are a two approaches to getting data from an Acurite bridge: sniff or listen.

### Sniff

### Listen

## Credits

Rich of Modern Toil did some of the first packet sniffing and decoding:

    `http://moderntoil.com/?p=794`

George Nincehelser figured out some crucial parts of the encoding and fed it into weewx:

    `http://nincehelser.com/ipwx/`

Perhaps the first weewx driver was a sniffer called haculink:

    `http://geekfun.com/hackulink/`

Now that packet sniffing is much more common, and thanks to the lack of any magic numbers, the protocol used by the July 2016 firmware was decoded within weeks by many different people.