Twitter helps you create and share ideas and information instantly, without barriers.

This is an extension to weewx that tweets weather data.

### Credentials

You must obtain oauth credentials for your weather station.  Login to twitter at

http://dev.twitter.com/apps

where you can create a new app and obtain the credentials.

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-twitter-0.12.tgz

### How to Install

1.  Install the Twitter Python bindings:

    ```
sudo pip install twython
```

2.  Run the extension installer:

    ```
wee_extension --install weewx-twitter.x.y.tgz
```

3.  Modify weewx.conf:

    ```
    [StdRESTful]
        [[Twitter]]
            app_key = APP_KEY
            app_key_secret = APP_KEY_SECRET
            oauth_token = OAUTH_TOKEN
            oauth_token_secret = OAUTH_TOKEN_SECRET
```

4.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Options

The message contents are controlled by the _format_ option.  For example, to display only the temperature, use this format:

```
[StdRESTful]
    [[Twitter]]
        format = "temperature: {outTemp:%.2f}"
```

The default format contains observations common to most weather stations:

```
[StdRESTful]
    [[Twitter]]
        format = "{station:%.8s}: Ws: {windSpeed:%.1f}; Wd: {windDir:%03.0f}; \
Wg: {windGust:%.1f}; oT: {outTemp:%.1f}; oH: {outHumidity:%.2f}; \
P: {barometer:%.3f}; R: {rain:%.3f}"
```

By default, units are those specified in the StdConvert section of weewx.conf.  To use other units, use the _unit_system_ option:

```
[StdRESTful]
    [[Twitter]]
        unit_system = METRICWX    # US, METRIC, or METRICWX
```

### Upgrading from weewx 2.6-2.7

Simply run the extension installer then restart weewx.  If your weewx.conf already contained twitter credentials, these should be remembered by the installer.