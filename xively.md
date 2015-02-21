The Internet of Things was an idea. Now it’s a reality. Right now, developers and companies are connecting devices and apps to securely store and exchange data. It’s the one solution that brings big ideas about the world to the world.

This is an extension to weewx that uploads weather data to xively (formerly pachube, formerly cosm).

### Screenshot
![xively](http://lancet.mit.edu/mwall/projects/weather/weewx-on-xively.png)

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-xively-0.4.tgz

### How to Install

1.  Run the extension installer:

    ```
setup.py install --extension weewx-xively-x.y.tgz
```

2. Modify weewx.conf:

    ```
    [StdRESTful]
        [[Xively]]
            token = TOKEN
            feed = FEED_ID
```

3.  Restart weewx:

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Options

_append_units_label_ - Indicates whether to append the units abbreviation to the variable name.  For example, `outTemp` would be called `outTemp_F` and `pressure` would be called `pressure_mbar`.  Default is `True`.

_unit_system_ - Unit system to which values should be converted before uploading.  If nothing is specified, the units from StdConvert will be used.  Possible values are `US`, `METRIC`, or `METRICWX`.  Default is None.

### Upgrading from weewx 2.6-2.7

Simply run the extension installer then restart weewx.  If your weewx.conf already contained a token and feed, these should be remembered by the installer.

### Upgrading from weewx 2.5

If you were using the cosm.py extension, remove it - it has been replaced by xively.py.  In weewx.conf, rename the COSM section to Xively and remove the 'driver' parameter.