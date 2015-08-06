Some weather station hardware is designed to work with additional sensors.  For example, the KlimaLogg Pro can use up to 8 temperature/humidity sensors, the Vantage hardware can work with additional temperature/humidity sensors or soil moisture sensors, and the Hideki TE923 stations support up to 5 remote temperature/humidity sensors.

With WeeWX you can add any number of sensors, of any type, to any weather station hardware.  The data will not be displayed on the weather station console, but they will be displayed in WeeWX reports.

These are instructions for adding data from a single sensor.

### How to do it

1.  Save the sensor data to a file, say /var/tmp/pond.txt

    ```
23.4
```

2.  Add a service that parses the file.  For a setup.py installation this would be something like /home/weewx/bin/user/pond.py

    ```
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
        except Exception, e:
            syslog.syslog(syslog.LOG_ERR, "pond: cannot read value: %s" % e)
```

3.  Tell weewx about the service by adding it to weewx.conf:

    ```
[Engine]
    [[Services]]
        data_services = ..., user.pond.PondService
```

4.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Other things to consider

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