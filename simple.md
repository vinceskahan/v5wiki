Simple is a single page of weather data.  A live example of this skin is here:

http://sailing.mit.edu/weather/simple

It also includes options for a light-on-dark variation:

http://sailing.mit.edu/weather/darkness

### Download

wget -O weewx-simple.zip https://github.com/matthewwall/weewx-simple/archive/master.zip

### Installation

1.  Run the extension installer:

```
weectl extension install weewx-simple.zip
```

2.  Restart weewx

```
sudo systemctl stop weewx
sudo systemctl start weewx
```

### Screenshots

The simple skin with light-on-dark colors.
![](http://lancet.mit.edu/mwall/projects/weather/weewx-simple-0.3/light-on-dark.png)

The simple skin with dark-on-light colors.
![](http://lancet.mit.edu/mwall/projects/weather/weewx-simple-0.3/dark-on-light.png)
