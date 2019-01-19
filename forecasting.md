With the forecast extension you can display forecasts for up to 10 days in the future as well as tide predictions.  Forecasts are either generated using the Zambretti method, or downloaded from the US National Weather Service (NWS), Weather Underground (WU), Open Weathermap, UK Met Office, Aeris Weather, World Weather Online, or Dark Sky.  Tide predictions are generated using XTide.

The forecast extension displays forecast data in many formats, including table, strip chart, iconic bars, compact summary, and meteograph.

The extension consists of services that download/generate forecast data, and a search list extension to make forecast data available within templates.  There is one service for each type of forecast, and a single search list extension to display data from any type of forecast.

The extension includes pre-configured .inc files that can be included in other templates.  This provides an easy way to add a strip, table, or iconic view to other skins.

A live example is available at the MIT sailing pavilion:

http://sailing.mit.edu/weather/forecast.html

The forecasts can be display in many different formats, no matter which forecasting service provided the data:

<table>
<tr valign="top">
<td>Compact<br/>
<img src="http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-compact.png" width="150"/>
</td>
<td>
Iconic<br/>
<img src="http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-iconic.png" width="150"/>
</td>
<td>
Iconic<br/>
<img src="http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-iconic-horizontal.png" width="150"/>
</td>
</tr>
<tr valign="top">
<td>
Table<br/>
<img src="http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-table.png" width="200"/>
</td>
<td colspan="2">
Strip<br/>
<img src="http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-strip.png" width="400"/>
</td>
</tr>
</table>

## Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-forecast-3.3.0.tgz

## Pre-Requisites

### Weather Underground (WU)

If you want forecasts from Weather Underground, visit the Weather Underground to get an api_key.  A free key at the lowest tier of service will enable you to download up to 500 forecasts per day.  By default, the forecast module downloads 8 forecasts per day.

[http://www.wunderground.com/weather/api/](http://www.wunderground.com/weather/api/)

### US National Weather Service (NWS)

If you want forecasts from the US National Weather Service, you must determine the location identifier and forecast office identifier for your location. The forecast office identifier indicates a general region, for example BOX for the eastern part of Massachusetts. The location identifier indicates a smaller area within a forecast office region, for example MAZ014 for the city of Cambridge, Massachusetts.

First figure out your 3-letter forecast office identifier (foid, also referred to as HADS) and 6-character location identifier (lid) by visiting the NWS web site:

http://www.nws.noaa.gov/oh/hads/USGS/

Test by requesting a point forecast using a url of this form in a web browser:

http://forecast.weather.gov/product.php?site=NWS&product=PFM&format=txt&issuedby=YOUR_THREE_LETTER_FOID

Scan the output for a description of your city or county and the associated location identifier. If there is more than one location with the same location identifier, then specify the town/county line that immediately follows the location identifier.

### OpenWeatherMap (OWM)

If you want forecasts from OpenWeatherMap, obtain an API key from the OpenWeatherMap web site.  Activation of an API key for Free and Startup plans takes 10 minutes. For other tariff plans it is 10 to 60 minutes.

http://openweathermap.org/appid

### UK Met Office (UKMO)

If you want forecasts from the UK Met Office, visit the DataPoint web site to obtain an API key.

http://metoffice.gov.uk/datapoint

### Aeris

If you want forecasts from Aeris, create an account at the Aeris web site then generate an API key.

http://www.aerisweather.com/account

### World Weather Online

If you want forecasts from World Weather Online, register for an account at the WWO web site then generate and API key.

https://developer.worldweatheronline.com/auth/register

### Dark Sky

If you want forecasts from Dark Sky, visit the darksky web site to obtain the API key.

https://darksky.net/dev/register

### xtide

If you want tide predictions, install the xtide software.

```
sudo apt-get install xtide
sudo apt-get install xtide-data
```

Tide data for Canada, Germany, and the UK are in a separate package:

```
sudo apt-get install xtide-data-nonfree
```

Test by generating predictions for your desired location

```
tide -l Boston
```

## Installation

1.  Run the extension installer to install the forecast service and sample skin.

```
wee_extension --install weewx-forecast-x.y.tgz
```

2.  In weewx.conf, specify required parameters for the forecast services you want to run

```
    [Forecast]
        [[NWS]]
            lid = MAZ014                 # specify a location identifier
            foid = BOX                   # specify a forecast office identifier
        [[WU]]
            api_key = XXXXXXXXXXXXXXXX   # specify a weather underground api_key
        [[OWM]]
            api_key = XXXXXXXXXXXXXXXX   # specify an open weathermap api_key
        [[UKMO]]
            api_key = XXXXXXXXXXXXXXXX   # specify a UK met office api_key
            location = 2337              # specify code for UK location
        [[Aeris]]
            client_id = XXXXXXXXXXXXXXXX      # specify client identifier
            client_secret = XXXXXXXXXXXXXXXX  # specify client secret key
        [[DS]]
            api_key = XXXXXXXXXXXXXXXX   # specify a dark sky api_key
        [[XTide]]
            location = Boston            # specify a location
```

3.  Restart weewx

```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

The extension installer will add all of the forecast methods to the service list.  However, each method requires parameters such as an API key, so unless those parameters are entered, only Zambretti will actually run.

Zambretti runs only once per day, and only in the morning.  So you might have to wait until the following day to see any Zambretti output.  WU and NWS run every 3-4 hours.  Tide forecasts are generated once every few weeks.

The installer also installs a skin called 'forecast' that illustrates how to use raw forecast data in any template, and how to use the pre-configured displays in other templates.

Monitor the log to see what is happening.  The log should include messages about each type of forecast as it starts.  After the first archive interval, each forecast should download/generate, and the forecast data should be displayed in the forecast skin.

## Customization

There are two approaches to using forecast data in your templates: (1) include a pre-configured `forecast_*.inc`, and/or (2) use the `$forecast` variables.

Whether you use an include or the `$forecast` variable directly, the ForecastVariables search list extension must be installed for the skin.

### The ForecastVariables search list extension

The forecast extension includes a search list extension called `ForecastVariables`.  This defines a variable called `$forecast` that you can use to embed fine-grained forecast information anywhere in a template, and that is used extensively in the forecast `forecast_*.inc` files.

To use the `$forecast` variable in a template, the ForecastVariables search list extension must be enabled within the skin for that template.  For example, to enable ForecastVariables in the Standard skin, modify `skins/Standard/skin.conf`:

```
[CheetahGenerator]
    search_list_extensions = user.forecast.ForecastVariables
    ...
```

As an example, see the `skin.conf` in the `forecast` skin.

### Include a file

Perhaps the easiest way to put forecasts into your pages is to include a file.  The forecast extension includes a few `.inc` files, each of which displays forecast information in a different format.  Screen shots of these formats are shown below.

For example, to use the iconic display in a template, simply include the iconic file and its corresponding CSS:
```
<html>
  <head>
    <title>forecast</title>
    <link rel='stylesheet' type='text/css' href='forecast_iconic.css'/>
  </head>
  <body>
#include "forecast_iconic.inc"
  </body>
</html>
```
Optionally adjust the parameters in skin.conf (restart of weewx is not required):
```
[Extras]
    [[forecast_iconic_settings]]
        source = WU
        num_days = 7
```
Or set them in weewx.conf (restart of weewx is required):
```
[StdReport]
    [[forecast]]
        [[[Extras]]]
            [[[[forecast_iconic_settings]]]]
                source = WU
                num_days = 7
```
If you include more than once in a single template, you probably want to specify the settings before each include.  This lets you have multiple forecasts in a single page.  For example, this would show two iconic forecasts in a single page, one from Weather Underground and one from the US National Weather Service.
```
<html>
  <head>
    <title>two forecasts</title>
    <link rel='stylesheet' type='text/css' href='forecast_iconic.css'/>
  </head>
  <body>
#set global $forecast_iconic_settings = dict()
#set global $forecast_iconic_settings['source'] = 'WU'
#set global $forecast_iconic_settings['num_days'] = 10
#include "forecast_iconic.inc"

#set global $forecast_iconic_settings = dict()
#set global $forecast_iconic_settings['source'] = 'NWS'
#set global $forecast_iconic_settings['num_days'] = 7
#include "forecast_iconic.inc"
  </body>
</html>
```

### Options for included files

Each of the included files has options that control the forecast display.  For example, the strip can be oriented vertically or horizontally, or you might want to hide the snow forecast in tables if you live in a region where it never snows.

These are the options for the various include files (the files that end in .inc such as forecast_table.inc):
```
[forecast_table_settings]
    source = NWS
    num_periods = 72
    show_legend = 1
    show_hourly = 0
    show_day = 1
    show_date = 1
    show_outlook = 1
    show_temp = 1
    show_dewpoint = 0
    show_humidity = 0
    show_wind = 1
    show_tides = 1
    show_sun = 0
    show_moon = 0
    show_pop = 1
    show_precip = 1
    show_obvis = 1

[forecast_strip_settings]
    source = Aeris
    num_periods = 300
    show_temp = 1
    show_temp_min = 1
    show_temp_max = 1
    show_dewpoint = 0
    show_humidity = 0
    show_wind = 1
    show_clouds = 1
    show_pop = 1
    show_snow = 1
    show_precip = 1
    show_sun = 0
    show_moon = 0
    show_moonphase = 0
    show_tides = 0

[forecast_iconic_settings]
    source = WU
    orientation = vertical
    num_days = 7
    bar_width = 150
    show_date = 0
    show_pop = 0
    show_precip = 1
    show_obvis = 1

[forecast_compact_settings]
    source = WU
    num_days = 4
    show_wind = 1
    show_pop = 1
    show_precip = 1
    show_obvis = 1
```

### Forecast variables

You can insert parts of a forecast into any template using the `$forecast` variable.  For example, you might want to display the predicted high and low temperatures next to the current temperature, or you might want to show the rain predicted each day for the next week.

Here is an example of one way to use the `$forecast` variable in a template.

First query the database for the forecast events:
```
#set $periods = $forecast.weather_periods('WU', max_events=6)
```
The first argument is the forecast source, such as 'WU', 'WWO', or 'NWS'.  Optionally specify the maximum number of events you are interested in.  If not specified, the query will return every possible event for the indicated source.

Each period contains forecast data such as temperature, sky cover, chance of precipitation, and obstructions to visibility.  Each period also contains a timestamp and location.

For example, this would display the time, temperature, and sky cover for each period:
```
#for $period in $periods
$period.event_ts $period.temp $forecast.label('Weather', $period.clouds)<br/>
#end for
```
For details about how to use the `$forecast` variables, see the comments at the beginning of forecast.py.


### Forecast icons

The forecasts include many icons, which must be copied to any skin that will use the forecast.  For example, if you want to embed the forecast in the Standard skin, first copy the icons:

```
cp -rp skins/forecast/icons skins/Standard
```

Then include the icons in the list of files that should be copied by the CopyGenerator.  Append the icons directory to the `copy_once` parameter in the CopyGenerator configuration in `skins/Standard/skin.conf`:

```
[CopyGenerator]
    copy_once = ..., icons/*.png
```

If you want to change the look-and-feel of a forecast, replace the images in the icons directory.  For example, you might want colorful images instead of the default monochromatic set.  Use the same set of filenames and be sure to include all the different images.

## Screenshots

### forecast_iconic
The iconic forecast is a simplified view with time increasing down the page (vertical) or across the page (horizontal).
```
#include "forecast_iconic.inc"
```
<table>
<tr><td>
<img src="http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-iconic.png"/>
</td><td width="50px">
</td><td>
<img src="http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-iconic-horizontal.png"/>
</td></tr>
</table>

### forecast_compact
The compact forecast shows a few days into the future.
```
#include "forecast_compact.inc"
```
![compact](http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-compact.png)

### forecast_table
The forecast table contains a lot of information, with time increasing into the future as you look down the table.  Each day is a summary of the forecast information for that day, and can be expanded to see the forecast breakdown for the day.  For some forecast sources this is hourly, for others it is 4-hourly, for others it is a mix.  Use the settings to specify which columns should be displayed.
```
#include "forecast_table.inc"
```
![table](http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-table.png)

### forecast_strip
The forecast strip contains detailed forecast data, with time increasing into the future as you scroll to the right.  The period duration depends on the forecast source - some are hourly, some are a mix of hourly and 4-hourly.  Use the settings to specify which rows should be displayed.
```
#include "forecast_strip.inc"
```
![strip](http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-strip.png)

### Embedded data

Forecast data embedded in the Standard skin.

![Standard subset](http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-default.png)

Forecast data embedded in the exfoliation skin.

![current](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/current-with-forecast-annotated.png)

## Caveats

The forecasting module was distributed as a part of weewx 2.5.  As of weewx 2.6 it is a separate download.

See the comments in forecast.py for complete details about this extension.
