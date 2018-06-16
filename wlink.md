### WeatherLink Driver

WeatherLink is a web service provided by Davis to collect weather data from Davis weather stations.
This is a driver that collects data from WeatherLink web sites.

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-wlink-0.14.tgz

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
        username = USERNAME # use the device ID for weatherlink 2.0 accounts
        password = PASSWORD
        driver = user.wlink
```

3.  Start weewx

```
sudo /etc/init.d/weewx start
```

### Options

Use `rain_bucket_type` to specify the rain bucket type.  Possible values are `0`, `1`, or `2` for 0.01 inch, 0.2 mm, or 0.1 mm bucket, respectively.

```
[WeatherLink]
   ...
   rain_bucket_type = 0
```

### Caveats

This driver obtains LOOP data by polling the weatherlink.com site every 60 seconds.  ARCHIVE data are retrieved from the weatherlink.com site using the API published by Davis.

http://www.davisnet.com/support/weather/download/VantageSerialProtocolDocs_v261.pdf

According to the FAQ, LOOP data are uploaded once per minute, and weatherlink.com creates an archive record each hour:

http://www.davisnet.com/support/weather/faq/index.asp?ProdFam=15

Non-archive records downloaded from weatherlink.com contain only a subset of observations: windSpeed, windDir, barometer, outTemp, and rain.