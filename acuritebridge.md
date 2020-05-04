# How to get data from an Acurite bridge

<img src="http://www.weewx.com/hardware/908wes.png" width="200" align="right"/>

The Acurite bridge listens for radio signals from Acurite sensors such as the 5-in-1, Tower, and Temperature/Humidity.  Then it transmits sensor data via a wired connection to a TCP/IP network.

When it was first introduced in 2013, the bridge sent data to the web service `acu-link.com`.

In July 2016, Acurite introduced the `myacurite.com` web service and new firmware to make the bridge device work with it.  The transition to new firmware took place from September to December 2016.  The new firmware sends data to `myacurite.com` and optionally to the weather underground.

The bridge has a small web server on port 80, but it displays only its status and configuration, as well as an option to turn the LEDs on or off.  It does not display sensor readings.

<img src="http://www.weewx.com/hardware/acurite-bridge.png" width="200" align="right"/>

The bridge can detect many different types of Acurite sensors, such as the 5-in-1, the Tower, and various types of Temperature/Humidity sensors.

The bridge appears to send data for every sensor that it detects.  However, the `myacurite.com` web service supported only up to 10 devices.

In early 2018, Acurite announced that they would no longer support the SmartHub.  After some outcry from users, Acurite postponed the shutdown from 2018 to 2019.  As of February 2019, the Acurite servers are no more, leaving SmartHubs to flail about looking for a response from the servers.  But have no fear!  You can continue to capture data from any bridge/SmartHub device by running the weewx-interceptor driver.  The weewx-interceptor responds as the Acurite servers did, so that SmartHub has no idea that it is no longer supported by its maker.

## Strategies

There are a two approaches to getting data from an Acurite bridge: sniff or listen.

### Sniff

In the sniff approach, a computer monitors the local network for traffic from the Acurite bridge.  Packets from the bridge are decoded then fed into weeWX.  The sniffing must be done on a device that can see the network traffic from the Acurite bridge.  For example, it can happen on the network router, a computer connected to the same hub or port-mirroring switch as the Acurite bridge, or on a device that bridges the Acurite bridge to the rest of the network.

### Listen

In the listen approach, the bridge is fooled into sending data to a computer that appears to be myacurite.com.  Data sent by the bridge are decoded then fed into weeWX.

This is typically implemented by creating a local DNS entry that maps the acurite server to a the network address of a local computer.  Then weeWX runs on the local computer, receiving and decoding the data sent by the bridge.  In a slight variation of this approach, a web server on the local computer runs a PHP script to receive the data, then the PHP script transfers the data to weeWX, typically by an intermediate file.

## Some details about how the bridge works

There are two known firmware versions for the bridge: 126 (in use until 2016) and 224 (introduced in 2016).  To find out which firmware is running on your bridge, point a web browser at the bridge.  That will also show you which sensors the bridge can detect.

Apparently the smarthub/bridge will not report any sensor data unless it has established a connection with one of the acurite servers (or something that responds like an acurite server).  I tested this behavior with a couple of smarthubs - one still running the old firmware (126) and one running the newer firmware (224).  Occasionally i would see a sensor in the smarthub's web interface, but for the most part the devices are useless until they talk to the mothership.

The old servers used to reply to the smarthub with a simple string:
```
{"success":1, "checkversion":"126"}
```
The newer firmware expects a timestamp - probably to ensure that the smarthub time is ok - so the newer servers used to reply with this string:
```
{"localtime":"00:00:00", "checkversion":"224"}
```
If an older hub receives a version number other than 126, then it tries to update the firmware.  Presumably the newer firmware would also try to update if it gets version number other than 224, but I have not tested that.

After that initial response from the server, the smarthub starts sending data (in either wu format or chaney format, depending on the firmware version).  Those are the packets that we capture in order to collect data.

The interceptor driver will handle these cases so that any smarthub just thinks that the interceptor is an acurite server.

So if your smarthub is running the older firmware, be sure to set the `firmware_version=126` parameter, otherwise your hub will probably hang, trying to install the newer firmware (the interceptor does not know the protocol for sending newer firmware).

Acurite shut down the `myacurite.com` and `acu-link.com` servers, and put `127.0.0.1` in as the dns entry for them.  So any smarthub/bridge that attempts to connect to them will resolve the name(s) to localhost, then do nothing, since it will obviously not respond to itself.  The bridge is not broken - it just has no one to talk to.

If you use the interceptor to collect data, this means that now you *must* run the interceptor in listen mode, not sniff mode.  In sniff mode, the interceptor does not interact with the smarthub, so the smarthub hangs when it tries to contact itself (localhost).  In listen mode, the interceptor looks like an acurite server, so the smarthub responds and data flows.

So you must make a DNS entry for `hubapi.myacurite.com` and `www.acu-link.com` that points to the machine running the interceptor, and you must ensure that traffic on port 80 goes to the interceptor.  The easiest way is to put the DNS override in your router's configuration, and make the interceptor listen on port 80.  But if you already have a web server running on port 80 on the same computer as the interceptor, then you'll have have to set up a reverse proxy on the web server, or run the web server on a different port, or one of many other options explained in the interceptor readme.

By the way, you can continue to use smarthub with new acurite sensors - at least until acurite changes their RF protocols.  The smarthubs still have a many years of life left in them, even though acurite has abandoned those who purchased them.  Acurite is not likely to change the RF protocols, since even the latest (2019) wifi consoles still know how to talk to the sensors that shipped with the bridge.

The remote sensors are pretty cheap - I've seen them for as little as $5 each at walmart.  Although you could skip the bridge altogether by using a USB software-defined radio (SDR), in some cases it might be easier/cheaper to use a bridge. (This article focusses on extending the life of an Accurite Bridge. SDR is more generalised, as it is brand-agnostic, but it is more challenging as it requires writing Sensor Maps. richardh9935)

## What does it look like?

If you enter the URL to the smarthub in a web browser, you should see a status page.  Here are examples from the different firmware versions.

<table>
<tr valign="top"><td>
<p>firmware 126</p>
<img src="http://www.weewx.com/cfg/acurite-bridge-fw-126.png" width="200"/>
</td><td>
<p>firmware 224</p>
<img src="http://www.weewx.com/cfg/acurite-bridge-fw-224.png" width="200"/>
</td></tr>
</table>

## Credits

The interceptor driver in listen mode is the easiest way to capture data from a bridge/smarthub:

    http://github.com/matthewwall/weewx-interceptor

Rich of Modern Toil did some of the first packet sniffing and decoding:

    http://moderntoil.com/?p=794

George Nincehelser figured out some crucial parts of the encoding and fed it into weewx:

    http://nincehelser.com/ipwx/

Perhaps the first weewx driver was a sniffer called haculink:

    http://geekfun.com/hackulink/

Now that packet sniffing is much more common, and thanks to the lack of any magic numbers, the protocol used by the July 2016 firmware was decoded within weeks by many different people.