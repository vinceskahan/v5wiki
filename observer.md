## How to get data from an Observer weather station

<img src="http://weewx.com/hardware/wh2600.png" align="right" height="100"/>
<img src="http://weewx.com/hardware/hp1000.png" align="right" height="100"/>

Fine Offset makes an all-in-one weather station, with data reported either on a console, console-to-internet, or bridge-to-internet.  These stations are sold by Ambient using the brand Observer.

Fine Offset model numbers include HP1000 (uploads via console with wifi), HP1003 (console display no uploads), and WH2600 (uploads via bridge).

Ambient model numbers include WS080 (uploads via bridge), WS1400 (uploads via bridge), WS1200 (uploads via bridge), and WS1001 (uploads via console).

This is a description of how to get the data from the weather station directly, instead of relying only on the station uploading to Weather Underground.

### Configure the console to send to weewx

If the console has a web interface, simply use that to change the destination.  Enter the hostname or IP address of a local computer running the weewx-interceptor driver.  Data will be sent directly to weewx.

If the console does not have a web interface, then modify the `server.ini` file in the console.  Enter the hostname or IP address of a local computer running the weewx-interceptor driver.  Data will be sent directly to weewx.

### If configuring the console or bridge is not possible...

If the console or bridge cannot be configured, then things get a bit more complicated.  You can capture the traffic using one of these approaches:

Hijack DNS.  Create a local DNS entry on your router so that when the console or bridge tries to contact Weather Underground, it gets the IP address of the computer running weewx.

HTTP Proxy.  Install an HTTP proxy so that any HTTP traffic leaving your network can be intercepted.  Create a proxy rule to capture every packet sent from the console or bridge, and redirect those packets to the computer running weewx.

Packet Capture.  Run tcpdump, ngrep, or similar utility to capture all traffic from the console or bridge, and redirect it to the computer running weewx.

Detailed instructions for DNS, Proxy, and Packet Capture are included in the weewx-interceptor <a href="https://github.com/matthewwall/weewx-interceptor">readme</a>.
