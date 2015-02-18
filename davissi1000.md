Using weewx with a DavisSi1000 radio
------------------------------------

You can use weewx with a 900MHz Davis ISS weather station without using a Davis console by using a 3DRobotics 900MHz telemetry radio, along with the DavisSi1000 radio firmware.

More details on where to get the radio and how to set it up is available in the [DavisSi1000 github repository](http://github.com/tridge/DavisSi1000).

Setting up weewx for DavisSi1000
--------------------------------

A DavisSi1000 radio will output weather data from your ISS as a series of JSON formatted messages on the radios TTL serial port at 57600.

Each line will look like this:

    { "transmitter_id": 2, "RSSI": 118, "recv_packets": 3952, "lost_packets": 4, "bad_CRC": 7, "wind_speed_mph": 0, "wind_direction_degrees": 356,   "temperature_F": 66.86, "humidity_pct": 54.9, "light": 356, "rain_spoons": 13, "raw": "22 00 FC 4D 02 A0 E2 43 FF FF", "version": "1.0" }

You can then use the radio with weewx by enabling the Si1000.py driver. The latest version of the driver is in the [DavisSi1000 github repository](https://github.com/tridge/DavisSi1000/blob/origin/weewx/Si1000.py).

The following config in your weewx.conf would enable the Si1000 driver:

    [Station]
     station_type = Si1000

    [Si1000]
     driver = weewx.drivers.Si1000
     port = /dev/ttyAMA0
     baudrate = 57600

     # adjust wind direction based on install orientation
     wind_dir_adjust = 180
