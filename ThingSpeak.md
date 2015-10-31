ThingSpeak is "The open data platform for the Internet of Things."

This is an extension to weewx that uploads weather data to thingspeak.

### Screenshot

![screenshot](http://lancet.mit.edu/mwall/projects/weather/weewx-on-thingspeak.png)

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-thingspeak-0.6.tgz

### How to Install

1.  Run the extension installer:

    ```
wee_extension --install weewx-thingspeak-x.y.tgz
```

2.  Modify weewx.conf:

    ```
    [StdRESTful]
        [[ThingSpeak]]
            api_key = API_KEY
```

3.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Options

_unit_system_ - Unit system to which values should be converted before uploading.  If nothing is specified, the units from StdConvert will be used.  Possible values are `US`, `METRIC`, or `METRICWX`.  Default is None.

_fields_ - Each thingspeak api_key is bound to a channel, and each channel has at most 7 fields.  The _fields_ option is a map that specifies which observations will upload as each of the 7 fields.  For example, this map would upload `outTemp` as `field1` with units of degree Fahrenheit, and `inTemp` as `field2` with units of degree Celsius.  The _units_ are optional.

```
[StdRESTful]
    [[ThingSpeak]]
        api_key = TOKEN
        [[[fields]]]
            [[[[field1]]]]
                obs = outTemp
                units = degree_F
            [[[[field2]]]]
                obs = outTemp
                units = degree_C
```

The default map is:

```
[StdRESTful]
    [[ThingSpeak]]
        api_key = TOKEN
        [[[fields]]]
            [[[[field1]]]]
                obs = outTemp
            [[[[field2]]]]
                obs = outHumidity
            [[[[field3]]]]
                obs = windSpeed
            [[[[field4]]]]
                obs = windDir
            [[[[field5]]]]
                obs = windGust
            [[[[field6]]]]
                obs = barometer
            [[[[field7]]]]
                obs = rain
```