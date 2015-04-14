<img src='http://assets.windfinder.com/images/prod/v1/featured_products/forecast.png' width='100' align='right'/>

### WindFinder

http://www.windfinder.com

Wind, waves and weather for kitesurfers, windsurfers, surfers and sailors
This is an extension to weewx that uploads weather data to WindFinder.

### Credentials

You must register with WindFinder to get login credentials before you can upload data.  Visit the WindFinder contribution page at:

http://www.windfinder.com/weather-station/add.htm#meteobridge

There you will enter details about your weather station, then obtain a station identifier and password.

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-windfinder-0.7.tgz

### How to Install

1.  Run the extension installer:

    ```
    setup.py install --extension weewx-windfinder-x.y.tgz
```
2.  Modify weewx.conf:

    ```
    [StdRESTful]
        [[WindFinder]]
            station_id = STATION_ID
            password = PASSWORD
```

3.  Restart weewx:

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Upgrading from weewx 2.6-2.7

Simply run the extension installer then restart weewx.  If your `weewx.conf` already contained a `station_id` and `password`, these should be remembered by the installer.