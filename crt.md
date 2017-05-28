### Cumulus Real-Time

This is an extension to weewx that emits data to a file in the format used by Cumulus realtime.txt.  The format for the realtime.txt file is maintained at the cumulus web site:

http://wiki.sandaysoft.com/a/Realtime.txt

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-crt-0.18.tgz

### How to Install

1.  Run the extension installer:

```
wee_extension --install weewx-crt-x.y.tgz
```

2.  Modify weewx.conf:

```
    [CumulusRealTime]
        filename = /var/tmp/realtime.txt
        unit_system = METRIC # options are US, METRIC, METRICWX
```

3.  Restart weewx

```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Verify

The realtime.txt file should be modified on each LOOP.  For some stations the update will happen more often than others.  For example, Vantage sensors update as often as every 2.5 seconds, whereas FineOffset stations never update more often than 48 seconds.

The file should contain something like this:

```
25/01/15 05:53:40 -1.3 80 -4.3 9.8 9.0 225 0.0 0.0 989.7 SW 2 km/h C mb cm
8.2 2.2 3.4 3.4 0.0 20.6 40 -5.8 -0.6 -1.0 05:15 -1.3 05:50 10.6 05:35
18.5 05:35 989.7 05:35 989.3 05:05 3.0.1 0 14.6 -1.3 -4.4 NULL NULL NULL
249 0.0 0 0 0 WSW 384 meter -4.6 0.2 0.0 0
```

### Options

_binding_ - The binding determines whether the file will be updated with every LOOP packet or archive record.  Possible values are `loop` or `archive`.  Default is `loop`

_filename_ - The full path of the file.  Default is `/var/tmp/realtime.txt`

_date_separator_ - The character for separating day, month, and year.  Default is `/`

_none_ - The string to use when there is no value.  Default is `NULL`

_sunny_threshold_ - The cutoff value that determines whether the sun is shining.  The threshold is a value between 0 and 1, inclusive.  It is compared against the ratio of actual solar radiation and theoretical maximum solar radiation to determine if the weather is sunny.  Default is 0.75.

_unit_system_ - The unit system to use in realtime.txt.  Options include `US`, `METRIC`, and `METRICWX`.  Default is to use the unit system of the weewx database.

_wind_units_ - The units to use for wind-related data in realtime.txt.  Options include `meter_per_second`, `mile_per_hour`, `km_per_hour`, and `knot`.  Default is to use the units defined in the unit system.

_temperature_units_ - The units to use for temperature data in realtime.txt.  Options include `degree_C` and `degree_F`.  Default is to use the units defined in the unit system.

_pressure_units_ - The units to use for pressure data in realtime.txt.  Options include `hPa`, `mbar`, and `inHg`.  Default is to use the units defined in the unit system.
             
_cloudbase_units_ - The units to use for cloud base in realtime.txt.  Options include `foot` and `meter`.  Default is to use the units for altitude in the unit system.
