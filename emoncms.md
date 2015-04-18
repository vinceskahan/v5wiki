Emoncms is a powerful open-source web-app for processing, logging and visualising energy, temperature and other environmental data.

This is an extension to weewx that uploads weather data to emoncms.

### Screenshot
![screenshot](http://lancet.mit.edu/mwall/projects/weather/weewx-on-emoncms.png)

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-emoncms-0.10.tgz

### How to Install

1.  Run the extension installer:

    ```
setup.py install --extension weewx-emoncms-x.y.tgz
```

2.  Modify weewx.conf:

    ```
    [StdRESTful]
        [[EmonCMS]]
            token = TOKEN
```

3.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Options

_node_ - The `node` is an integer that indicates a location in the data topology used by emoncms.  The `node` is required for emoncms 8.4 and later.  Default is `None`.

_prefix_ - Indicates whether to prepend each variable name with the indicated prefix.  For example, with `prefix = stationName`, `outTemp` would be `stationName_outTemp`.  Default is `None`.

_append_units_label_ - Indicates whether to append the units abbreviation to the variable name.  For example, `outTemp` would be called `outTemp_F` and `pressure` would be called `pressure_mbar`.  Default is `True`.

_unit_system_ - Unit system to which values should be converted before uploading.  If nothing is specified, the units from StdConvert will be used.  Possible values are `US`, `METRIC`, or `METRICWX`.  Default is None.

### Upgrading from weewx 2.6-2.7

Simply run the extension installer then restart weewx.  If your weewx.conf already contained a token, it should be remembered by the installer.

### Upgrading from weewx 2.5

If you were using the emoncms extension with weewx 2.5.x, be sure to remove the 'driver' parameter from the EmonCMS section in weewx.conf.