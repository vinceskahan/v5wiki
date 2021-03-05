# Meteo-Services Institute Weather and Climateanalyses

https://stations.meteo-services.com

More than 20 years of experience in marine-, agrarian- and media- weather forecasting give us the ability to provide you with the most precise and applicable weather service and data base for your weather predictions.

In our independent data processing service center we calculate the weather forecast 4 times a day. On base of the actual measurement values of more than 40.000 measurement stations worldwide our optimised numerical weather models (NMMB-Europe, WRF-Europe, ETA-Europe, WW3) provide regional and local weather predictions worldwide. 

If  you are planning a trip, a tour, a sailing trip or other activities - detailed weather-forecasts will influence your planning.

Do you want to be sure of your plans so that your equipment and your choice are suitable to the weather?
Meteo-Services provides weather forecasts and all relevant weather-data at a glance – sun, clouds, rain, snow, wind speed, wave forecasts and much more.

Actual more then 200 (Q1/2021) stations are working with our special-build receiver based on a raspberry pi receiving DAVIS Station signals directly. We also built solar powered stations with GSM and GPS receiving DAVIS Station data building an agriculture network and also a network for sailors here. Next step is going with LORA / LORAWLAN networks. Here we are in BETA-field tests. 

The MeteoServices extension (formerly weather365) for weeWX will send data to our network. This extension allows weeWX to send data to the MeteoServices (formerly weather365) network. 

The service is free. We use your weather data for model-control, development and climate-analyses / climate-predictions.  

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

Changelog - Milestones

<Station-Report> 
We are able to provide a individual Station-Report - Existing stations are still connected. Every new station will automatically connected to a special local Station-Report. 

Example Station-Report with LIVE WEATHER and LIVE_Prediction 

STATION: Neufahrn / Freising - near Munich Airport (Germany/Bavaria)  German and English Bulletin is available 
https://stations.meteo-services.com/wetterstation/index.php?station_id=1   

Mar/2021 - improving WebApp - new Template and JS
Feb/2021 - complete new graphics in stations-report
Jan/2021 - adding new interactive graphics showing the measured values 
Dec/2020 - adding new DB-Params for future use ... soil-params ... (air quality beta)
Nov/2020 - preparing Grasland with new interactive graphic
Oct/2020 - improving Archive
Sep/2020 - Archive - Functions new setup
Jul/2020 - alle graphs switch to jpgraph-Library
Jun/2020 - building WebApp - responsive ready design
May/2020 - making stations site - responsive Layout
Apr/2020 - Adding Huglin-Index 
Mar/2020 - adding corn-Index / (German: Maisreifeindex / Wäremsummen für Mais) 
Feb/2020 - improving text forecasts - adding "fog-recognition with local measured data" (test)
Jan/2020 - abdingt GTS - GraslandTemperature Vegetation Start /agriculture) / (German: Grünlandtemperaturindex) 
Dec/2019 - adding local text forecast (German-region) responding to local measured values and local models
Oct/2019 - adding switches metric / US  mm/inch etc.
May/2019 - adding station measurements to WRF-Europe in testmode
Jan/2019 - adding first local forecasts 
Dez/2018 - connect first data to local model forecasts / first Test with ETA
Sep/2018 - adding warnings
Jun/2018 - Version 1 - special station-report
Feb/2018 - some DB improvements
Jan/2018 - adding Synopsis
Oct/2017 - adding Metar
Jun/2017 - first setup with own stations (10) experimental network for agriculture in Bavaria

--

**HOW TO INSTALL - quick**

a) Install Meteo-Services -uploader in "Batch-mode" with one line.

On raspberry / linux Systems open a terminal (ctrl+alt+F1) or SSH session and as the user 'pi’ run: 

`bash <(curl -sL https://stations.meteo-services.com/wxupdates/update-weewx.sh)`

This installation-method works for Debian- and Python-installed weewx-systems with standard path.

**(If you’re running the installation over SSH, running the installation through a screen or tmux session is highly recommended.)**
Assuming everything went well, your data will be sent to stations.meteo-services.com and the first dataset is visible after approx 5 or 10 minutes.
You only have to enter your stationod - received by E-Mail

Find [StdRESTful] and add your station ID    replace xxxxxxxxxx with the stationid send by E-mail
`[[Weather365]]`
`stationid = xxxxxxxxxx`
`password = 14991234912`

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



