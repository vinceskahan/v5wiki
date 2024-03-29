Emoncms is a powerful open-source web-app for processing, logging and visualising energy, temperature and other environmental data.

This is an extension to weewx that uploads weather data to emoncms.

### Screenshot
![screenshot](http://lancet.mit.edu/mwall/projects/weather/weewx-on-emoncms.png)

### Download

wget -O weewx-emoncms.zip https://github.com/matthewwall/weewx-emoncms/archive/master.zip

### How to Install

1.  Run the extension installer:

```
weectl extension install weewx-emoncms.zip
```

2.  Modify weewx.conf:

```
    [StdRESTful]
        [[EmonCMS]]
            token = TOKEN
```

3.  Restart weewx

```
sudo systemctl stop weewx
sudo systemctl start weewx
```

### Options

_node_ - The `node` is an integer that indicates a location in the data topology used by emoncms.  The `node` is required for emoncms 8.4 and later.  Default is `None`.

_prefix_ - Indicates whether to prepend each variable name with the indicated prefix.  For example, with `prefix = stationName`, `outTemp` would be `stationName_outTemp`.  Default is `None`.

_append_units_label_ - Indicates whether to append the units abbreviation to the variable name.  For example, `outTemp` would be called `outTemp_F` and `pressure` would be called `pressure_mbar`.  Default is `True`.

_unit_system_ - Unit system to which values should be converted before uploading.  If nothing is specified, the units from StdConvert will be used.  Possible values are `US`, `METRIC`, or `METRICWX`.  Default is None.

_inputs_ - The name, units, and/or format can be customized for individual observations by listing them in a stanza called `inputs`.  Each stanza within the `inputs` stanza is the name of an observation.  Each observation stanza may include some or all of the keys `name`, `units`, and `format`.  If `name` is specified, that label will sent to EmonCMS for the observation.  If `units` is specified, the observation will be converted to those units before upload.  If `format` is specified, the observation will be converted to string using that format.

### Examples

This configuration will use the METRIC unit system for all observations, whether the database is METRIC, METRICWX, or US:

```
[StdRESTful]
    [[EmonCMS]]
        unit_system = METRIC
```

This configuration will specialize the units of outTemp and the units, format, and name of inTemp:

```
[StdRESTful]
    [[EmonCMS]]
        unit_system = METRICWX
        [[[inputs]]]
            [[[[outTemp]]]]
                units = degree_F
            [[[[inTemp]]]]
                units = degree_F
                format = %.3f
                name = inside_temperature
```

The observation `outTemp` will be converted to degrees F and uploaded as `outTemp_F`.  The observation `inTemp` will be converted to degrees F and uploaded as `inside_temperature` with 3 decimal places of precision.  Since the `unit_system` is `METRIC`, other temperatures (if they exist) will use degrees C.  For example `extraTemp1` will be uploaded as `extraTemp1_C`.

### Upgrading from weewx 2.6-2.7

Simply run the extension installer then restart weewx.  If your weewx.conf already contained a token, it should be remembered by the installer.

### Upgrading from weewx 2.5

If you were using the emoncms extension with weewx 2.5.x, be sure to remove the 'driver' parameter from the EmonCMS section in weewx.conf.
