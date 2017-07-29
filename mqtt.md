MQTT is a machine-to-machine (M2M)/"Internet of Things" connectivity protocol. It was designed as an extremely lightweight publish/subscribe messaging transport. It is useful for connections with remote locations where a small code footprint is required and/or network bandwidth is at a premium. For example, it has been used in sensors communicating to a broker via satellite link, over occasional dial-up connections with healthcare providers, and in a range of home automation and small device scenarios. It is also ideal for mobile applications because of its small size, low power usage, minimised data packets, and efficient distribution of information to one or many receivers.

This is an extension to weewx that uploads weather data to an MQTT broker (server).

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-mqtt-0.15.tgz

You can use WGET to download it to your PI using the following:

_Please note that we are running in the HOME directory! /home/pi/_


```
wget http://lancet.mit.edu/mwall/projects/weather/releases/weewx-mqtt-0.15.tgz
```




### How to Install

1.  Install the MQTT Python bindings:

```
sudo pip install paho-mqtt
```
_If you get an error: "**pip: command not found**", then run "**sudo apt-get install python-setuptools**" first, then run "**sudo easy_install pip**" this would resolve the issue. If not? well try google!_


2.  Run the extension installer:

```
wee_extension --install weewx-mqtt-0.15.tgz
```

3.  Modify **[StdRestful]** in **"/etc/weewx/weewx.conf"** (Around line 71) and add the following lines: 

```
    [[MQTT]]
        server_url = mqtt://username:password@localhost:1883/
        topic = weather
        unit_system = METRIC
```

At the bottom in **"/etc/weewx/weewx.conf"** find **[ [Services] ]**, change or add the following

```
restful_services = user.mqtt.MQTT
```

4.  Restart weewx

```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Options

_append_units_label_ - Indicates whether to append the units abbreviation to the variable name.  For example, `outTemp` would be called `outTemp_F` and `pressure` would be called `pressure_mbar`.  Default is `True`.

_unit_system_ - Unit system to which values should be converted before uploading.  If nothing is specified, the units from StdConvert will be used.  Possible values are `US`, `METRIC`, or `METRICWX`.  Default is None.

_binding_ - Indicates whether to bind to LOOP packets ('loop') or archive records ('archive').  Default is 'archive'.

_topic_ - The MQTT topic under which to publish.  Default is 'weather'.

_aggregation_ - How the observations should be grouped.  Options are `individual` and/or `aggregate`.  When `individual` is specified, each observation is sent as a separate MQTT message.  When `aggregate` is specified, all observations are sent in a single, JSON-formatted message.  Default is `individual, aggregate`, i.e., send a message for each observation as well as an aggregate message with all observations.

_retain_ - When set to `True`, the MQTT `retain` property is set for each message.  Default is `False`.

### Upgrading from weewx 2.6-2.7

Simply run the extension installer then restart weewx.  If your weewx.conf already contained a server_url and topic, these should be remembered by the installer.


### MQTT Output

When connecting to MQTT subscribe to the following:

```
weather/#
```
_Please note that "weather" is the default topic, and may vary from your topic setting_

This will list all observations


Example for individual observations:

```
weather/loop
weather/cloudbase_meter
weather/outHumidity
weather/pressure_mbar
weather/rain_cm
weather/barometer_mbar
weather/dewpoint_C
weather/rainTotal
weather/ptr
weather/illuminance
weather/heatindex_C
weather/dayRain_cm
weather/outTempBatteryStatus
weather/delay
weather/inDewpoint
weather/altimeter_mbar
weather/windchill_C
weather/appTemp_C
weather/outTemp_C
weather/status
weather/windGust_kph
weather/humidex_C
weather/rain24_cm
weather/rxCheckPercent
weather/hourRain_cm
weather/inTemp_C
weather/windSpeed_kph
weather/usUnits
weather/UV
weather/rainRate_cm_per_hour
weather/dateTime
weather/windDir
weather/inHumidity
weather/radiation_Wpm2
```
_Please note that this list may vary depending on your settings_

