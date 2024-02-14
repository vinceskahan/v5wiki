<img src='https://cdn.windfinder.com/prod/images/favicons/mstile-144x144.af7269d8.png' width='100' align='right'/>

### WindFinder

http://www.windfinder.com

Wind, waves and weather for kitesurfers, windsurfers, surfers and sailors
This is an extension to weewx that uploads weather data to WindFinder.

### Credentials

You must register with WindFinder to get login credentials before you can upload data.  Visit the WindFinder contribution page at:

http://www.windfinder.com/weather-station/add.htm#meteobridge

There you will enter details about your weather station, then obtain a station identifier and password.

### Download

wget -O weewx-windfinder.zip https://github.com/matthewwall/weewx-windfinder/archive/master.zip

### How to Install

1.  Run the extension installer:

```
weectl extension install weewx-windfinder.zip
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
sudo systemctl stop weewx
sudo systemctl start weewx
```

### Upgrading from weewx 2.6-2.7

Simply run the extension installer then restart weewx.  If your `weewx.conf` already contained a `station_id` and `password`, these should be remembered by the installer.
