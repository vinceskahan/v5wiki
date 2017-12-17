The OpenWeatherMap service provides free weather data and forecast API suitable for any cartographic services like web and smartphones applications. Ideology is inspired by OpenStreetMap and Wikipedia that make information free and available for everybody. OpenWeatherMap provides wide range of weather data such as map with current weather, week forecast, precipitation, wind, clouds, data from weather Stations and many others. Weather data is received from global Meteorological broadcast services and more than 40 000 weather stations.

This is an extension to weewx that uploads weather data to OpenWeatherMap.

## Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-owm-0.7.tgz

## How to Install

1.  Run the extension installer:

    ```
    wee_extension --install weewx-owm-x.y.tgz
    ```

2.  Modify weewx.conf:

    ```
    [StdRESTful]
        [[OpenWeatherMap]]
            appid = OWM_APPID
            station_id = STATION_ID
    ```

3.  Restart weewx

    ```
    sudo /etc/init.d/weewx stop
    sudo /etc/init.d/weewx start
    ```

## Changes to the OpenWeatherMap API

The current OpenWeatherMap API is version 3.0.  Perhaps the most significant change introduced in the 3.0 API is a timestamp - if your weather station has a data logger, historical records will be uploaded to OWM during catchup.  This was not possible in the pre-3.0 API.

However, if you would like to use the pre-3.0 API, use version 0.3 of the weewx-owm extension:

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-owm-0.3.tgz

It uses a station_name, username, and password like this:
```
    [StdRESTful]
        [[OpenWeatherMap]]
            username = USERNAME
            password = PASSWORD
            station_name = STATION_NAME
```
OpenWeatherMap has not indicated how long the pre-3.0 API will be supported, but as of December 2016 it is still working.

## OpenWeatherMap 3.0 API: How to get your station registered
Another change to the 3.0 API is that you can no longer just supply a username and a password. You may have to `POST` to the API manually with something like [Postman](http://www.getpostman.com) or `curl` to register your station. As the steps on OWM are a little vague, just to put it lightly, here are the steps you must take:

1. [Sign up](https://home.openweathermap.org/users/sign_up) at OpenWeatherMap.
2. Generate an API key on your [account page](https://home.openweathermap.org), by clicking the "API keys" tab, type in a name in the box to the right, and clicking the "Generate" button. This key will usually work within 60 minutes, so be prepared for that.
3. Using `Postman` do the following (or the equivalent using `curl`):

    - Select "POST" where it says "GET".
    - Type in this URL: `http://api.openweathermap.org/data/3.0/stations?appid=YOUR-API-KEY-HERE`
    - Press the "Params"-button and, if they are not already filled in, type in `appid` in key-field, and your API key in the value-field.
    - Select the "Headers"-tab below, and type in "Content-Type" in the key-field, and "application/json" in the value-field.
    - In the "Body"-tab, check the "raw" radio button and an editor will open up where you will need to type in/replace the following:
        ```json
        {
            "external_id": "SF_UPD001",
            "name": "San Francisco Updated Station",
            "latitude": 37.80,
            "longitude": -122.47,
            "altitude": 143
        }
        ```
        - `"external_id"` needs to be a unique ID for your weather station.
        - `"name"` is the name of your weather station.
        - `"latitude"`, `"longitude"`, and `"altitude"` should reflect the values you already have in your weewx config.

4. Press "Send", and you should get a response at the bottom of the window, containing your ID, a `"created_at"` and a `"updated_at"`-timestamp, the data you entered above, and a rank.
5. Copy that ID in to your weewx config, along with your API key, as shown in step 2 under "Installation" above.
6. Restart weewx.
7. Wait until OpenWeatherMap picks up your data.


### Upgrading from weewx 2.6-2.7

Simply run the extension installer then restart weewx.  If your weewx.conf already contained a username and password, it should be remembered by the installer.

### Upgrading from weewx 2.5

If you were using the owm extension with weewx 2.5.x, be sure to remove the 'driver' parameter from the OpenWeatherMap section in weewx.conf.
