To see a live example of this skin:

http://sailing.mit.edu/weather

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-exfoliation-0.33.tgz

### Install

1.  Run the extension installer:

    ```
setup.py install --extension weewx-exfoliation-x.y.tgz
```

2.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Customize

The links page includes forecasts, satellite, and radar images.  Look
at the Extras section of the skin.conf file, then override any URLs with those
appropriate for your location.

### Screenshots
![forecast](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/current-with-forecast.png )
![exfoliation](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/day.png)
![year](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/year.png)
![almanac](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/almanac.png )
![links](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/links.png )
![forecast](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/forecast.png )
