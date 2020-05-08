### Smart Energy Groups

> As of May 2020, Smart Energy Groups appears to be gone.  This page remains as an example of how one company designed an API for communicating telemetry.

Smart Energy Groups provides real time energy information, beautiful visualisations, alerts and notifications, iPhone, iPad and Android widgets, rock solid performance, and open source hardware to help people save money and energy in real time.

This is an extension to weewx that uploads weather data to smart energy groups.

### Screenshot

![weewx-on-seg](http://lancet.mit.edu/mwall/projects/weather/weewx-on-seg.png)

### Download

wget -O weewx-seg.zip https://github.com/matthewwall/weewx-seg/archive/master.zip

### How to Install

1.  Run the extension installer:

```
wee_extension --install weewx-seg.zip
```

2.  Modify weewx.conf:

```
    [StdRESTful]
        [[SmartEnergyGroups]]
            token = TOKEN
            node = station_name
```

3.  Restart weewx

```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Options

_prepend_seg_label_ - Indicates whether to prepend the type identifier defined by SEG, such as `p_`, `e_`, or `temperature_`, to help automatically configure data streams on the SEG web site.  For example, `outTemp` would be called `temperature_outTemp`.  Default is `True`.

_append_units_label_ - Indicates whether to append the units abbreviation to the variable name.  For example, `outTemp` would be called `outTemp_F` and `pressure` would be called `pressure_mbar`.  Default is `True`.

_unit_system_ - Unit system to which values should be converted before uploading.  If nothing is specified, the units from StdConvert will be used.  Possible values are `US`, `METRIC`, or `METRICWX`.  Default is None.

### Upgrading from weewx 2.6-2.7

Simply run the extension installer then restart weewx.  If your weewx.conf already contained a token, it should be remembered by the installer.  The parameter _station_ has been replaced by _node_.

### Upgrading from 2.5

If you were using the seg extension with weewx 2.5.x, be sure to remove the 'driver' parameter from the SmartEnergyGroups section in weewx.conf.