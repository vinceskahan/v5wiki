Using weewx with a DavisSi1000 radio
------------------------------------

You can use weewx with a 900MHz Davis ISS weather station, without using a Davis console, by using a 3DRobotics 900MHz telemetry radio, along with the DavisSi1000 radio firmware.

For general information on using an Si1000 wireless receiver, see [this thread on wxforum.net](http://www.wxforum.net/index.php?topic=18718.0).

More details on where to get the radio and how to set it up is available in the [DavisSi1000 github repository](http://github.com/tridge/DavisSi1000).

Installing the DavisSi1000 driver
--------------------------------

The latest version of the DavisSi1000 driver is in the [DavisSi1000 github repository](https://github.com/tridge/DavisSi1000/blob/origin/weewx/Si1000.py).

[Download the driver](https://raw.githubusercontent.com/tridge/DavisSi1000/origin/weewx/Si1000.py), and put it in your `user` subdirectory.

Then add the following new section to your `weewx.conf` file. It will tell weewx where to find your new driver, what port and baudrate to use, and what the orientation of your ISS is. Make sure you don't add the section in the middle of some other existing section. 

```
[Si1000]
    driver = user.Si1000
    port = /dev/ttyAMA0
    baudrate = 57600

    # adjust wind direction based on install orientation
    wind_dir_adjust = 180
```

Then tell weewx to use the new driver by changing the option `station_type`

```
[Station]
    .
    .
    .
    station_type = Si1000
    .
    .
    .
```

Note: depending on your ISS orientation, you may have to change the `wind_dir_adjust` parameter.
