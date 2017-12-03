WeatherCloud is a real-time weather social network formed by observers from around the world. Weathercloud is free and open to anyone.

This is an extension to weewx that uploads weather data to WeatherCloud.

http://weathercloud.net

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-wcloud-0.11.tgz

### How to Install

1.  Run the extension installer:

```
wee_extension --install weewx-wcloud-x.y.tgz
```

2.  Modify weewx.conf.  Find the WeatherCloud section then enter the weathercloud id and key that you obtained from weathercloud.

```
[StdRESTful]
    [[WeatherCloud]]
        id = WEATHERCLOUD_ID
        key = WEATHERCLOUD_KEY
```

3.  Restart weewx

```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Upgrading from weewx 2.6-2.7

Simply run the extension installer then restart weewx.  If your weewx.conf already contained a weathercloud id and key, these should be remembered by the installer.