# Meteo-Services Institute Weather and Climateanalyses

https://stations.meteo-services.com

More than 20 years of experience in marine weather forecasting give us the ability to provide you with the most precise and applicable weather service and data base for your weather predictions.

In our independent data processing service center we calculate the weather forecast 4 times a day. On base of the actual measurement values of more than 40.000 measurement stations worldwide our optimised numerical weather models (WRF/ETA, WW3) provide exact weather predictions worldwide. 

If  you are planning a trip, a tour, a sailing trip or other activities - detailed weather-forecasts will influence your planning.

Do you want to be sure of your plans so that your equipment and your choice are suitable to the weather?
Meteo-Services provides weather forecasts and all relevant weather-data at a glance – sun, clouds, rain, snow, wind speed, wave forecasts and much more.

We have more then 100 stations working with our new receiver based on a raspberry pi receiving DAVIS Station signals directly.

We have also built solar powered stations with GSM and GPS receiving DAVIS Station data building an agriculture network and also a network for sailors here. 

The Weather365 extension for weeWX will send data to our network.  This extension allows weeWX to send data to the Weather365 network. 

The service is free. We use your weather data for model-control and development. 

**We give each registered station owner access to our professional network - PROFESSIONAL account at meteo-services.com where we run and show our own forecast models.**
**How to get the PROFESSIONAL account? Please register your weatherstation and start sharing your data with us. Note your stationid. Sign up / Create a new account on meteo-services.com . Send us email (info@meteo-services.com) with your username and your stationid.**
**We will check and set you´re new account to PROFESSIONAL for free when sharing your data with us.**

The extension is available at:
```
* Update 2020-08-20 - new version 3.0 (fixing some problems - minimize log-files) 
* and will work with Python3 now - sorry for the delay in the last weeks
[WEATHER365 - Uploader - Extension](https://stations.meteo-services.com/wxupdates/EXTENSION/ ) 
```
[WEATHER365 - Uploader Extension (python3)](https://stations.meteo-services.com/wxupdates/EXTENSION/)
Obtain an ID by registering a new station:
```
https://stations.meteo-services.com/login/
```

Existing stations can login here:
```
https://stations.meteo-services.com/login/login.php
```

NEW <Station-Report> 
Now we are able to provide a Station-Report - Existing stations are still connected. Every new station will automatically connected to a special Station-Report. 

Example Station-Report with LIVE WEATHER and LIVE_Prediction 

STATION: Neufahrn / Freising - near Munich Airport (Germany/Bavaria)  German and English Bulletin is available 
https://stations.meteo-services.com/wetterstation/index.php?station_id=1   

**HOW TO INSTALL - quick**
a) Install Meteo-Services - -uploader

On raspberry / linux Systems open a terminal (ctrl+alt+F1) or SSH session and as the user 'pi’ run: This installation-method works for Debian- and Python-installed weewx-systems with standard path.

`bash <(curl -sL https://stations.meteo-services.com/wxupdates/update-weewx.sh)`

(If you’re running the installation over SSH, running the installation through a screen or tmux session is highly recommended.)
Assuming everything went well, your data will be sent to stations.meteo-services.com and the first dataset is visible after approx 5 or 10 minutes.

**b) manual install Meteo-Services - -uploader**

Download

`https://stations.meteo-services.com/wxupdates/Downloads/weather365-3.0.zip`

Installation raspberry-pi or Linux

Login on your raspberry / Linux locally or remote . On raspberry try it as Standard-User „pi“.

This install describes a Debian based install.

If you´re on a **python2 system** please install package python-future at first ! 
ex. install for Linux/raspberry systems stretch / buster 

`sudo apt-get update `
`sudo apt-get install python-future`

If you´re on a **python3 system **please install package python3-future at first ! 
ex. install for Linux/raspberry systems stretch / buster 

`sudo apt-get update `
`sudo apt-get install python3-future`

1. Fetch the Uploader ( using Terminal Window )

`wget https://stations.meteo-services.com/wxupdates/Downloads/weather365-3.0.zip`

2. Install the update

`sudo wee_extension --install weather365-3.0.zip`

3. extend weewx.conf

Add here -> [StdRESTful] weewx.conf …

`sudo nano /etc/weewx/weewx.conf`

Find [StdRESTful] and add your station ID    replace xxxxxxxxxx with the stationid send by E-mail

`[[Weather365]]`
`stationid = xxxxxxxxxx`
`password = 14991234912`

SAVE with CTRL-X

4. Restart weewx ( Sometimes you have to restart twice )

`sudo /etc/init.d/weewx stop`
`sudo /etc/init.d/weewx start`



