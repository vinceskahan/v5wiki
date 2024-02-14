### WeatherLink Driver

WeatherLink is a web service provided by Davis to collect weather data from Davis weather stations.
This is a driver that collects data from WeatherLink web sites.

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-wlink-0.4.tgz

### How to Install

1.  Copy the driver file to the weewx user directory:

    ```
cp wlink-x.y.z/bin/user/wlink.py /home/weewx/bin/user
```

2.  Enter the configuration in weewx.conf:

    ```
    [Station]
        ...
        station_type = WeatherLink

    [WeatherLink]
        username = USERNAME
        password = PASSWORD
        driver = user.wlink
```

3.  Start weewx

    ```
sudo systemctl start weewx
```

### Caveats

This driver obtains LOOP data by polling the weatherlink.com site every 60 seconds.  ARCHIVE data are retrieved from the weatherlink.com site using the API published by Davis.

http://www.davisnet.com/support/weather/download/VantageSerialProtocolDocs_v261.pdf