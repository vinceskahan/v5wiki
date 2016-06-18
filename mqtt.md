MQTT is a machine-to-machine (M2M)/"Internet of Things" connectivity protocol. It was designed as an extremely lightweight publish/subscribe messaging transport. It is useful for connections with remote locations where a small code footprint is required and/or network bandwidth is at a premium. For example, it has been used in sensors communicating to a broker via satellite link, over occasional dial-up connections with healthcare providers, and in a range of home automation and small device scenarios. It is also ideal for mobile applications because of its small size, low power usage, minimised data packets, and efficient distribution of information to one or many receivers.

This is an extension to weewx that uploads weather data to an MQTT broker (server).

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-mqtt-0.15.tgz

### How to Install

1.  Install the MQTT Python bindings:

    ```
sudo pip install paho-mqtt
```

2.  Run the extension installer:

    ```
wee_extension --install weewx-mqtt-x.y.tgz
```

3.  Modify weewx.conf:

    ```
    [StdRESTful]
        [[MQTT]]
            server_url = mqtt://username:password@example.com:1883
            topic = weather
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