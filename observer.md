## How to get data from a HP1000/WH2600 weather station

<img src="http://weewx.com/hardware/wh2600.png" align="right" height="100"/>
<img src="http://weewx.com/hardware/hp1000.png" align="right" height="100"/>
<img src="http://weewx.com/hardware/ws0900.png" align="right" height="100"/>

Fine Offset makes a wireless weather station, with data reported either on a console, console-to-internet, or bridge-to-internet.  These stations are sold by Ambient, Aercus, Froggit, and others.

These stations upload directly to weather underground.  Some firmware versions include an option to set the destination of the upload so that you can send directly to your own web site or web service.  The uploads are driven either by a bridge or a console, depending on the configuration.  The bridge receives signals from the sensors, then uploads via a wired ethernet connection.  The console receives signals from the sensors, then uploads via a wireless (wifi) connection.

Fine Offset model numbers include:
* HP1000 - uploads via console wifi
* HP1003 - console display, no uploads
* WH2600 - uploads via bridge

Ambient model numbers include:
* WS0800 - uploads via bridge (two T/H sensors, no wind/rain)
* WS0900 - uploads via bridge (two T/H, wind, rain, no console)
* WS1400 - uploads via bridge (same as WH2600)
* WS1200 - uploads via bridge (WH2600 with console)
* WS1001 - uploads via console (same as HP1000)

Aercus models include:
* WeatherSleuth - uploads via bridge (same as WH2600)
* WeatherRanger - uploads via console wifi (same as HP1000)

The station is also sold as the Maplin N23DQ.  It is sold in Australia as the XC0442.

Ambient uses the brand name *ObserverIP* for the bridge and the brand name *Observer* for the wifi and non-wifi consoles.

This is a description of how to get the data from the weather station directly, instead of relying only on the station uploading to Weather Underground.

### Configure the console to send to weewx

If the console has a web interface, simply use that to change the destination.  Enter the hostname or IP address the computer running the weewx-interceptor driver.  Data will be sent directly to weewx.

If the console does not have a web interface, then modify the `server.ini` file in the console.  Remove the SD card, modify server.ini, then replace the SD card.  Enter the hostname or IP address of the computer running the weewx-interceptor driver.  Data will be sent directly to weewx.

### Configure the bridge to send to weewx

It might be possible to telnet to the bridge.  If so, use `setdsthn host.example.com` followed by `saveconfig` where `host.example.com` is the computer on which the weewx-interceptor driver is running.  Data will be sent directly to weewx.

> **telnet 192.168.0.6**  
> Trying 192.168.0.6...  
> Connected to 192.168.0.6.  
> Escape character is '^]'.  
> username: **admin**  
> password:  
> Successful login through telnet  
> telnet> **setdsthn rtupdate.wunderground.com**  
> Ok  
> telnet> **saveconfig**  
> Saving Configuration to FLASH  
> Ok  
> telnet> **reboot**  
> Connection closed by foreign host.  

### If configuring the console or bridge is not possible...

If the console or bridge cannot be configured, then things get a bit more complicated.  You can capture the traffic using one of these approaches:

Hijack DNS.  Create a local DNS entry on your router so that when the console or bridge tries to contact Weather Underground, it gets the IP address of the computer running weewx.

HTTP Proxy.  Install an HTTP proxy so that any HTTP traffic leaving your network can be intercepted.  Create a proxy rule to capture every packet sent from the console or bridge, and redirect those packets to the computer running weewx.

Packet Capture.  Run `tcpdump`, `ngrep`, or similar utility to capture all traffic from the console or bridge, and redirect it to the computer running weewx.

Detailed instructions for DNS, Proxy, and Packet Capture are included in the weewx-interceptor <a href="https://github.com/matthewwall/weewx-interceptor">readme</a>.

### Warning!

The scraping approach can be unstable.  According to Pat O'Brien,

> I strongly recommend you do not advertise the scraping method. It is known to cause issues with the ObserverIP unit. The ObserverIP (black box with antenna on it) is an inexpensive unit that can barely survive an uptime of 30 days, let alone screen scraping every 15 seconds.

### References

dkm created the first weewx driver for these stations (https://github.com/dkmcode/weewx-observerip).  That driver has two modes: direct (screen scraping) and indirect (capture via php).  In direct mode, the driver grabs the web page from the station then parses it to get the weather data.  In indirect mode, the station must be configured to send data to the computer running weewx, a PHP script captures the data to file, then the driver reads data from the file.

mwall made minor changes to the dkm driver to handle different firmware versions (https://github.com/matthewwall/weewx-observerip).

Pat O'Brien created a screen scraping driver, then a socket-based driver.  His experiences are documented at http://obrienlabs.net/redirecting-weather-station-data-from-observerip/ and the code for his 'SocketLogger' driver is at https://github.com/poblabs/weewx-socketlogger

mwall created the weewx-interceptor driver, which has two modes: listen or sniff.  In listen mode, the driver accepts direct connections from the weather station bridge or console.  In sniff mode, the driver captures traffic as the station sends it to weather underground.  (https://github.com/matthewwall/weewx-interceptor)
