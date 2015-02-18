## WeatherBug

Since 1993, WeatherBug, by Earth Networks, has been committed to delivering the most accurate weather information and the fastest weather alerts to our customers so they can Know Before™

This is an extension to weewx that uploads weather data to WeatherBug.

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-wbug-0.4.tgz

### How to Install

1.  Run the extension installer:

`setup.py install --extension weewx-wbug-x.y.tgz`

2.  Modify weewx.conf
~~~~
[StdRESTful]
    [[WeatherBug]]
        publisher_id = PUBLISHER_ID
        station_number = STATION_NUMBER
        password = PASSWORD3.  Restart weewx
~~~~
### Upgrading from weewx 2.6-2.7

Simply run the extension installer then restart weewx.  If your weewx.conf already contained publisher_id, station_number, and password, these should be remembered by the installer.

### Upgrading from weewx 2.5

If you were using the wbug extension with weewx 2.5.x, be sure to remove the 'driver' parameter from the WeatherBug section in weewx.conf.