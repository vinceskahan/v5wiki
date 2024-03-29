Some weather station hardware is designed to work with additional sensors.  For example, the KlimaLogg Pro can use up to 8 temperature/humidity sensors, the Vantage hardware can work with additional temperature/humidity sensors or soil moisture sensors, and the Hideki TE923 stations support up to 5 remote temperature/humidity sensors.

With WeeWX you can add any number of sensors, of any type, to any weather station hardware.  The data will not be displayed on the weather station console, but they will be displayed in WeeWX reports.

These are instructions for adding data from a single sensor.

### How to do it

1.  Save the sensor data to a file, say `/var/tmp/pond.txt`

    ```
    45.5
    ```

2.  Add a service to WeeWX that will parse the file.  For a setup.py installation this would be something like `/home/weewx/bin/user/pond.py`

    ```Python
    import syslog
    import weewx
    from weewx.wxengine import StdService

    class PondService(StdService):
        def __init__(self, engine, config_dict):
            super(PondService, self).__init__(engine, config_dict)      
            d = config_dict.get('PondService', {})
            self.filename = d.get('filename', '/var/tmp/pond.txt')
            syslog.syslog(syslog.LOG_INFO, "pond: using %s" % self.filename)
            self.bind(weewx.NEW_ARCHIVE_RECORD, self.read_file)
    
        def read_file(self, event):
            try:
                with open(self.filename) as f:
                    value = f.read()
                syslog.syslog(syslog.LOG_DEBUG, "pond: found value of %s" % value)
                event.record['extraTemp1'] = float(value)
            except Exception as e:
                syslog.syslog(syslog.LOG_ERR, "pond: cannot read value: %s" % e)
    ```

3.  Tell weewx about the service by adding it to weewx.conf:

    ```ini
    [Engine]
        [[Services]]
            data_services = ..., user.pond.PondService
    ```

4.  Restart weewx

    ```shell
    sudo systemctl stop weewx
    sudo systemctl start weewx
    ```

### Units

Note that our new service, `PondService`, is unaware of units. It blindly adds the contents of `pond.txt` to an existing record.

*You must make sure the units match!*

That is, whatever unit system you use in `pond.txt`, must match the unit system used by the incoming record.
The unit system used by the incoming record will be given by `event.record['usUnits']`.

See the table given by the [Appendix *Units*](http://www.weewx.com/docs/customizing.htm#units) in the *Customizing Guide* for the meaning of the various unit
system codes.

### Multiple sensors

If there are multiple sensors, then use a file format that identifies each sensor.  For example, you could use name value pairs:

```
extraTemp1=32.4
extraTemp2=21.5
```

or a comma-delimited format:

```
32.4,21.5
```

then modify the parsing section of the service to read multiple values.

### Database schema

If you use observation names that are not in the database schema, you must extend the database schema for these to be recorded.  See the WeeWX customization guide for details.
