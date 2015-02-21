With the forecast extension you can display forecasts for up to 10 days in the future as well as tide predictions.  Forecasts are either generated using the Zambretti method, or downloaded from the US National Weather Service (NWS) or Weather Underground (WU).  Tide predictions are generated using XTide.

The forecasting module consists of services that download/generate forecast data, and a search list extension to make forecast data available within templates.  There is one service for each type of forecast, and a single search list extension to display data from any type of forecast.

The forecasting module was distributed as a part of weewx 2.5.  As of weewx 2.6 it is a separate download.

See the comments in forecast.py for complete details about this extension.

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-forecast-3.0.2.tgz

### Pre-Requisites

*   Weather Underground API Key

If you want forecasts from Weather Underground, visit the Weather Underground to get an api_key.  A free key at the lowest tier of service will enable you to download up to 500 forecasts per day.  By default, the forecast module downloads 8 forecasts per day.

[http://www.wunderground.com/weather/api/](http://www.wunderground.com/weather/api/)

*   xtide

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

*   NWS identifiers

If you want forecasts from the US National Weather Service, you must determine the location identifier and forecast office identifier for your location. The forecast office identifier indicates a general region, for example BOX for the eastern part of Massachusetts. The location identifier indicates a smaller area within a forecast office region, for example MAZ014 for the city of Cambridge, Massachusetts.

First figure out your 3-letter forecast office identifier (foid, also referred to as HADS) and 5-character location identifier (lid) by visiting the NWS web site:

http://www.nws.noaa.gov/oh/hads/USGS/

Test by requesting a point forecast using a url of this form in a web browser:

http://forecast.weather.gov/product.php?site=NWS&product=PFM&format=txt&issuedby=YOUR_THREE_LETTER_FOID

Scan the output for a description of your city or county and the associated location identifier. 

### Automated Installation

1.  Run the extension installer to install the forecast service and sample skin.

    ```
setup.py install --extension weewx-forecast-x.y.tgz
```

2.  In weewx.conf, specify required parameters for the forecast services you want to run

    ```
    [Forecast]
        [[WU]]
            api_key = INSERT_API_KEY
        [[NWS]]
            lid = INSERT_LOCATION_ID
            foid = INSERT_FORECAST_OFFICE_ID
        [[XTide]]
            location = INSERT_LOCATION
```

3.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

The extension installer will enable the WU, NWS, XTide, and Zambretti forecasts.  However, unless parameters are entered for WU, NWS, and XTide, only Zambretti will run.  The installer also installs a skin called 'forecast' that illustrates how to use forecast data in any template.

Zambretti runs only once per day, and only in the morning.  So you might have to wait until the following day to see any Zambretti output.  WU and NWS run every 3-4 hours.  Tide forecasts are generated once every few weeks.

Monitor the log to see what is happening.  The log should include messages about each type of forecast as it starts.  After the first archive interval, each forecast should download/generate, and the forecast data should be displayed in the forecast skin.

### Manual Installation

1.  Copy the forecast.py file to the weewx user directory:

    ```
cp forecast-x.y.z/bin/user/forecast.py /home/weewx/bin/user
```

2.  Make the following changes to weewx.conf

    Specify the forecast database in a new [Forecast] section.

    ```
    [Forecast]
        data_binding = forecast_binding
```

    Specify the data binding by adding a section to [DataBindings]:

    ```
    [DataBindings]
        ...
        [[forecast_binding]]
            database = forecast_sqlite
```

    Define the forecast database by adding a section to [Databases].  This is where forecast data will be stored.

    ```
    [Databases]
        ...
        [[forecast_sqlite]]
            root = %(WEEWX_ROOT)s
            database_name = archive/forecast.sdb
            driver = weedb.sqlite
```

    Extend the search_list by adding ForecastVariables to the CheetahGenerator section of the skin in which you would like to display forecast data.

    ```
    [StdReport]
        ...
        [[StandardReport]]
            ...
            [[[CheetahGenerator]]]
                search_list_extensions = user.forecast.ForecastVariables
```

3.  Now follow the instructions in the next sections for each type of forecast you would like to enable.

### Manual Installation: Zambretti Forecasts

The Zambretti forecast is a very general prediction for the next 6 to 12 hours, based on recent pressure, temperature, and wind conditions.  It is supposed to be about 90% accurate.  Try running the Zambretti forecast on your station to see how accurate it is for your area!

1.  Make the following changes to weewx.conf

    Enable the ZambrettiForecast service by appending to the archive service list.

    ```
    [Engines]
       archive_services = ... , user.forecast.ZambrettiForecast
```

2.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

3.  Display Zambretti forecast data

### Manual Installation: Weather Underground Forecasts

This is how to configure the hourly 10-day Weather Underground forecast.

1.  Make the following changes to weewx.conf

    Specify the Weather Underground api_key.

    ```
    [Forecast]
        ...
        [[WU]]
            api_key = KEY
```

    Enable the WUForecast service by appending to the archive service list.

    ```
    [Engines]
        archive_services = ... , user.forecast.WUForecast
```

2.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

If everything is working properly, you should see something like this in the log file after the next archive record:

```
Oct 12 14:21:27 saga weewx[28915]: forecast: WUThread: WU: got 240 forecast records
```

The forecast will download every 3 hours.  Forecasts older than 7 days will be deleted.3.  Display Weather Underground forecast data

### Manual Installation: NWS Forecasts

This is how to configure the US National Weather Service (NWS) forecasts.

1.  Make the following changes to weewx.conf

    Specify the location identifier (lid) and forecast office identifier (foid)

    ```
    [Forecast]
        ...
        [[NWS]]
            lid = LOCATION_ID
            foid = FORECAST_OFFICE_ID
```

    Enable the NWSForecast service by appending to the archive service list.

    ```
    [Engines]
        archive_services = ... , user.forecast.NWSForecast
```

2.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

If everything is working properly, you should see something like this in the log file after the next archive record:

```
Oct 12 14:21:27 saga weewx[28915]: forecast: NWSThread: NWS: got 40 forecast records
```

The forecast will download every 4 hours.  Forecasts older than 7 days will be deleted.3.  Display NWS forecast data

### Manual Installation: Tide Predictions

Tide predictions are made using the xtide tool.  XTide uses data from harmonics files to calculate the tides.

1.  Make the following changes to weewx.conf

    Specify the location in the [Forecast] section.  This tells weewx where to save the forecasts.  The location tells xtide where the tides should be predicted.  If the location contains a comma, then put quotes around it, for example, "Bangor, Northern Ireland".

    ```
    [Forecast]
        ...
        [[XTide]]
            location = Boston
```

    Enable the XTideForecast service by appending to the archive service list.

    ```
    [Engines]
        archive_services = ... , user.forecast.XTideForecast
```

2.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

3.  Display tide data

### Screenshots

![Standard subset](http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-default.png)

Forecast data embedded in the Standard skin.

![Standard full](http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-full.png)

Full table of forecast data embedded in the Standard skin.

![current](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/current-with-forecast-annotated.png)

Forecast data embedded in the exfoliation skin.

![forecast](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.22/forecast.png)

The forecast.html page from the exfoliation skin.
