# Collect data from the ecowitt GW1000 wifi bridge

This guide shows how to configure weeWX to collect data from the ecowitt GW1000 wifi bridge.  The GW1000 is manufactured by Fine Offset.  It receives 915MHz radio signals from a wide assortment of Fine Offset sensors, then sends the data via WiFi to various online services.  The bridge itself is small, powered by USB, and has no interface other than a single button and two LEDs.  It includes pressure, temperature, and humidity sensor at the end of an attached 1 meter wire.

## What does it look like?

<img src="gw1000-recipe/gw1000-front.JPG" width="200">
<img src="gw1000-recipe/gw1000-side.JPG" width="200">

## What do you need?

At the very least you need a GW1000.  Then you can add any number of sensors.  You will also need a computer on which to run weeWX.

| cost | description | example model | source |
|---|---|---|---|
| $20 | GW1000 | ecowitt | https://www.amazon.com/ECOWITT-Gateway-Temperature-Humidity-Pressure/dp/B07JLRFG24 |

The prices are US$ as of December 2019.

## Recipes

This recipe assumes that you have administrative access to the router, and that the router can provide name-to-IP-address lookup services (DNS).

### Configure the GW1000

Follow the instructions that came with the GW1000.  That basically boils down to:

1. Plug the GW1000 into a USB port.  The GW1000 only uses the USB port for power.

2. Install the WSView app on a phone.

3. Using the WSView app, connect to the GW1000 and configure it to use the local WiFi network.

4. Using the WSView app, add any additional sensors to the GW1000.

If those steps were successful, you should see at least live temperature, humidity, and pressure data from the GW1000.

The GW1000 will immediately start trying to send data to a server at ecowitt.net, at a frequency of about one attempt per minute.  You can change the frequency using the WSView app.  If the WiFi network is connected to the internet, the ecowitt.net server will reject the request with a response like this:

```
{"errcode":"40001","errmsg":"invalid passkey"}
```

If you happen to get an older GW1000, then the WSView app might prompt you immediately to upgrade the firmware on the GW1000.  You probably want firmware at least v1.5.5, since by then support for a wide variety of sensors had been added.  Firmware 1.4 was pretty anemic.

### Install weeWX

When you install weeWX, select `Simulator` when prompted for the station type.  You will change it later to `Interceptor` when you run the `wee_config --reconfigure` command.
```
# install weeWX
wget -qO - http://weewx.com/keys.html | sudo apt-key add -
wget -qO - http://weewx.com/apt/weewx.list | sudo tee /etc/apt/sources.list.d/weewx.list
sudo apt-get update
sudo apt-get install weewx

# shut down weeWX
sudo /etc/init.d/weewx stop

# install weewx-interceptor extension and enable the driver
git clone https://github.com/matthewwall/weewx-interceptor.git
sudo wee_extension --install weewx-interceptor
sudo wee_config --reconfigure
```

### Verify that the interceptor can receive data

In a terminal window, run the interceptor directly.  You must use `sudo` for this since the interceptor will listen on port 80; root permission is needed to listen on any port less than 1000.
```
sudo PYTHONPATH=/usr/share/weewx python3 /usr/share/weewx/user/interceptor.py --device=fineoffset-bridge --debug
```

Now enter a URL in a web browser.  This URL is typical of the URL that the GW1000 emits.  Replace the IP address `192.168.76.18` with the IP address of the computer running weeWX.
```
http://192.168.76.18/data/report?PASSKEY=AAAA7BE0B6C0FAD155BB6C7C01190EBD&stationtype=GW1000B_V1.5.5&dateutc=2019-12-29+16:27:27&tempinf=67.1&humidityin=39&baromrelin=30.138&baromabsin=30.138&freq=915M&model=GW1000
```

You should see a response from the interceptor something like this:
```
raw data: PASSKEY=AAAA7BE0B6C0FAD155BB6C7C01190EBD&stationtype=GW1000B_V1.5.5&dateutc=2019-12-29+16:27:27&tempinf=67.1&humidityin=39&baromrelin=30.138&baromabsin=30.138&freq=915M&model=GW1000
raw packet: {'humidity_in': 39.0, 'temperature_in': 67.1, 'barometer': 30.138, 'usUnits': 1, 'dateTime': 1577636847}
mapped packet: {'inHumidity': 39.0, 'barometer': 30.138, 'inTemp': 67.1, 'usUnits': 1, 'dateTime': 1577636847}
```


### Configure the router to hijack DNS queries

The easiest way to capture data is to make the GW1000 send data to weeWX instead of "the cloud".  This is done by "hijacking" the domain name system (DNS).  You must configure your network so that when the GW1000 asks for the IP address that goes with `rtpdate.ecowitt.net`, it gets the IP address of the computer running weeWX.

To do this, you need a router that is capable of providing name lookups.

For example, if your router is running 'OPNSense' or 'PFSense', navigate to the 'Services' > 'Unbound DNS' > 'Overrides' page.  Create a host override for Host rtpdate, Domain ecowitt.net, and IP address of the computer that is running weeWX.

You can also do this with 'Tomato' (or 'TomatoUSB' or 'FreshTomato'), 'DD-WRT', and 'OpenWRT' router firmwares.  If you are running your own 'bind' server, then make some local entries.  For examples, see the weewx-interceptor readme file.

<img src="gw1000-recipe/opn-sense-unbound.png" width="800">


### Verify the hijacking

After you configure the router, verify that the hijack is working.  Make a DNS lookup from any computer on your network.  When you look up `rtpdate.ecowitt.net`, you should get the IP address of the computer that is running weeWX.  The two most common tools for this are `nslookup` and `dig`.

```
# nslookup rtpdate.ecowitt.net
Server:		192.168.76.1
Address:	192.168.76.1#53

Name:	rtpdate.ecowitt.net
Address: 192.168.76.18       <- this is a local IP address, so the hijack worked!
```

If the interceptor is still running, then you should see data from the GW1000.  The frequency of data will depend on which sensors you have.

You might have to power cycle the GW1000 to make it see the new IP address.  Just unplug it, then plug it back in.  Data should start streaming after a minute or two.

### Verify the sensor mappings

The interceptor has a default `sensor_map` that should capture most of the sensor data.  However, if you deploy many sensors, or if you use a custom database schema, then you might have to modify the `sensor_map`.

Run the interceptor directly to see which sensors are recognized and which sensors are parsed.  From that you can figure out what you need to add to the `sensor_map`.


### Put the interceptor settings into the weeWX configuration

In the weeWX configuration file, modify the `[Interceptor]` section.  The typical configuration has the interceptor listening on port 80 on the default interface of the computer on which weeWX is running.

```
[Interceptor]
    driver = user.interceptor
    device_type = fineoffset-gw1000
```

### Start weewx

First run weeWX directly to ensure that the data collection is working properly, and that data are getting into the database and reports.  A minute or two after you start it, you should see `LOOP` packets that contain the data from the sensors, associated with database field names as defined in your `sensor_map`.  Every 5 minutes you should see an archive `RECORD` reported.

```
# run weewx directly to verify the data collection (ctrl-c to stop)
weewxd /etc/weewx/weewx.conf
```

After you have verified it is working properly, kill `weewxd`.  Now you can run it as a daemon so that it will continue to run, even after you log out of the raspberry pi.
```
# run weewx as a daemon and forget about it!
sudo /etc/init.d/weewx start
```

### Viewing the data and customizing the reports

WeeWX saves the data to a sqlite database at `/var/lib/weewx.sdb`.  You can browse the data directly by invoking the sqlite command-line tool:

```
sqlite3 /var/lib/weewx.sdb
```

In its default configuration, weeWX will put data into a report located at `/var/www/html`.  If you have a keyboard and monitor plugged into the weeWX computer, you can view the report directly in any web browser on that computer.

You can view the report remotely by installing a web server, such as `nginx`, `lighttpd`, or `apache`.

```
sudo apt-get install nginx
```

Then you can view the reports using a web browser on any computer/tablet/phone that can see weeWX computer:
```
http://<name-or-addr>/weewx
```

To customize the report or add other reports, see the weeWX customization guide:

http://weewx.com/docs/customizing.htm


## Troubleshooting

### Cannot bind to port 80

If there is already a web server running on the computer on which weeWX+interceptor will run, then you will not be able to run the interceptor directly on port 80.  A simple workaround is to turn off the web server.  If that is not possible, then you can add a reverse proxy configuration to the web server, so that when it gets an HTTP GET request from the GW1000, it proxies that request to weeWX+interceptor.  In this case, you would run the interceptor on a different port, say `localhost:8000`, then make the web server reverse proxy any `/data/report/` URL requests to that port.

For example, a reverse proxy configuration for nginx would look something like this:
```
location /data/report/ {
  proxy_set_header X-Forwarded-Host $host;
  proxy_set_header X-Forwarded-Server $host;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_pass http://localhost:8000;
}
```

This is also how you can run weeWX+interceptor as a user without root privileges.

### Sniff instead of Listen

If your router does not provide name service, or if you cannot manage your router, you'll have to use a sniffing approach.  Sniffing requires one of the following:

* run weeWX on a raspberry pi that bridges the WiFi and wired networks
* use a network hub, not a switch
* use a switch that can do port mirroring

See the weewx-interceptor readme for details.

### Additional sensors

The interceptor has a `LABEL_MAP` that associates the names in the HTTP GET request with the observation names that are mapped to the database fields.  If your sensors are not reporting, we might need to update the `LABEL_MAP` to recognize the sensor types.
