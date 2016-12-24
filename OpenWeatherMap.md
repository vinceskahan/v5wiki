The OpenWeatherMap service provides free weather data and forecast API suitable for any cartographic services like web and smartphones applications. Ideology is inspired by OpenStreetMap and Wikipedia that make information free and available for everybody. OpenWeatherMap provides wide range of weather data such as map with current weather, week forecast, precipitation, wind, clouds, data from weather Stations and many others. Weather data is received from global Meteorological broadcast services and more than 40 000 weather stations.

This is an extension to weewx that uploads weather data to OpenWeatherMap.

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-owm-0.5.tgz

### How to Install

1.  Run the extension installer:

    ```
wee_extension --install weewx-owm-x.y.tgz
```

2.  Modify weewx.conf:

    ```
    [StdRESTful]
        [[OpenWeatherMap]]
            appid = OWM_APPID
            station_id = STATION_ID
```

3.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Changes to the OpenWeatherMap API

If you would like to use the pre-3.0 OWM API, use 0.4 of the weewx-owm extension:

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-owm-0.4.tgz

It uses a station_name, username, and password like this:
```
    [StdRESTful]
        [[OpenWeatherMap]]
            username = USERNAME
            password = PASSWORD
            station_name = STATION_NAME
```

### Upgrading from weewx 2.6-2.7

Simply run the extension installer then restart weewx.  If your weewx.conf already contained a username and password, it should be remembered by the installer.

### Upgrading from weewx 2.5

If you were using the owm extension with weewx 2.5.x, be sure to remove the 'driver' parameter from the OpenWeatherMap section in weewx.conf.