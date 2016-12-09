With the forecast extension you can display forecasts for up to 10 days in the future as well as tide predictions.  Forecasts are either generated using the Zambretti method, or downloaded from the US National Weather Service (NWS), Weather Underground (WU), Open Weathermap, UK Met Office, Aeris Weather, or World Weather Online.  Tide predictions are generated using XTide.

The forecasting module consists of services that download/generate forecast data, and a search list extension to make forecast data available within templates.  There is one service for each type of forecast, and a single search list extension to display data from any type of forecast.

The extension includes pre-configured .inc files that can be included in other templates.  This provides an easy way to add a strip, table, or iconic view to other skins.

## Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-forecast-3.2.0.tgz

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

Scan the output for a description of your city or county and the associated location identifier. 

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
        [[XTide]]
            location = Boston            # specify a location
```

3.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

The extension installer will enable the WU, NWS, XTide, and Zambretti forecasts.  However, unless parameters are entered for WU, NWS, and XTide, only Zambretti will run.  The installer also installs a skin called 'forecast' that illustrates how to use forecast data in any template.

Zambretti runs only once per day, and only in the morning.  So you might have to wait until the following day to see any Zambretti output.  WU and NWS run every 3-4 hours.  Tide forecasts are generated once every few weeks.

Monitor the log to see what is happening.  The log should include messages about each type of forecast as it starts.  After the first archive interval, each forecast should download/generate, and the forecast data should be displayed in the forecast skin.

## Screenshots

Simple display of one-week forecast

![iconic](http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-iconic.png)

Forecast table

![table](http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-table.png)

Forecast strip

![strip](http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-strip.png)

Forecast data embedded in the Standard skin.

![Standard subset](http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-default.png)

Full table of forecast data embedded in the Standard skin.

![Standard full](http://lancet.mit.edu/mwall/projects/weather/weewx-forecast-full.png)

Forecast data embedded in the exfoliation skin.

![current](http://lancet.mit.edu/mwall/projects/weather/exfoliation-for-weewx-0.13/current-with-forecast-annotated.png)

## Caveats

The forecasting module was distributed as a part of weewx 2.5.  As of weewx 2.6 it is a separate download.

See the comments in forecast.py for complete details about this extension.
