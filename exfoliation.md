To see a live example of this skin:

http://sailing.mit.edu/weather

### Download

wget -O weewx-exfoliation.zip https://github.com/matthewwall/weewx-exfoliation/archive/master.zip

### Install

1.  Run the extension installer:

```
wee_extension --install weewx-exfoliation.zip
```

2.  Restart weewx

```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Customize

Customize the display by modifying parameters in skin.conf or overriding the parameters in weewx.conf.  A change to skin.conf does not require a restart of weewx - the change will show up the next time reports are generated.  A change to weewx.conf requires a restart of weewx.

For example, to change a parameter in skin.conf:
```
[Extras]
    current_show_radar = true
```
To override a parameter via weewx.conf:
```
[StdReport]
    [[exfoliation]]
        [[[Extras]]]
            current_show_radar = true
```

The skin includes pages for current conditions, forecast (if forecast extension is installed), history, almanac, station health (if cmon extension is installed), and links.

Each of these pages can be enabled or disabled.

For example, these parameters control which pages will be generated:
```                               
    show_forecast_page = true
    show_history_page = true
    show_almanac_page = true
    show_station_page = false
    show_links_page = true
```

These parameters show/hide elements on the `current` page:
```
    current_show_inside = true
    current_show_celestial = true
    current_show_tides = true
    current_show_radar = true
    current_show_forecast_summary = false
    current_show_forecast_table = true     
    current_forecast_source = WU
```

The links page includes forecasts, satellite, and radar images.  Look for the `links_*` parameters in skin.conf file, then override any URLs with those appropriate for your location.

For example, this sets the radar image to Bethel, Alaska:
```
    links_radar_local_img = https://radar.weather.gov/ridge/lite/N0S/ABC_loop.gif
```

The forecast page can display data from a single forecast source, or from multiple sources.  It can display the data in a single format, such as tabular, or multiple formats, including iconic, tabular, and strip.

For example, to display the Weather Underground, Aeris, and Open Weathermap forecasts in the iconic and tabular formats:
```
    forecast_sources = WU, Aeris, OWM
    forecast_formats = iconic, strip
```

See the `[Extras]` section in skin.conf for a complete list of options.

### Screenshots
![forecast](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/current-with-forecast.png )
![exfoliation](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/day.png)
![year](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/year.png)
![almanac](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/almanac.png )
![links](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/links.png )
![forecast](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/forecast.png )
