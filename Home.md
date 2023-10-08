## Welcome to the WeeWX wiki!

This is the place to go for information about the many extensions available for WeeWX, as well as random "how to" tips.  For information on how to download, install, and configure WeeWX, see the [WeeWX web site](http://www.weewx.com/).

Edit this page as you see fit; this wiki uses [Markdown](https://help.github.com/articles/github-flavored-markdown/) syntax, not HTML.  To add a new page, reference it within brackets, e.g.: [SamplePage].

:heavy_check_mark: = Tested with weewx4 and python3

### General
* [Posting to weewx-user](Help!-Posting-to-weewx-user)
* [Frequently-asked questions (FAQ)](WeeWX-Frequently-Asked-Questions)
* [Informationen auf Deutsch](https://www.woellsdorf-wetter.de/software/weewx.html)

### Basic principles
* [What you should know about paths](Understanding-paths)
* [What is the difference between `barometer`, `pressure`, and `altimeter`?](Barometer,-pressure,-and-altimeter)
* [What is `wind gust` and how is it different from `maximum wind speed`?](windgust)
* [Wind chill and heat indexes](Wind-chill-and-heat-index)
* [Watts/m&sup2; vs lux](Watts-and-lux)
* [Strategies for getting data into WeeWX](intake-strategies)
* [Strategies for publishing dashboards](dashboards)

### Hardware recommendations
* [On what hardware do you run WeeWX?](hardware)
* [Comparison of weather station hardware](http://weewx.com/hwcmp.html)
* [Switched USB hubs](usb-hubs)
* [Details on Fine Offset clones](https://www.wxforum.net/index.php?topic=40730.0)

### Guides (How to ...)

#### WeeWX configuration
* [Manage extensions](extensions)
* [Change from one driver to another](Change-driver)
* [Convert from setup.py to deb](How%20to%20convert%20from%20setup.py%20install%20to%20debian%20install)
* [Migrating `setup.py` installs to Version 5](v5-upgrade)
* [Colorize the UV index](Colored%20UV%20index)
* [Run multiple instances of WeeWX on one computer](weewx-multi)
* [Implement a multi-threaded service](multi-threaded-service)
* [Time services](Time-services)

#### Operating system configuration
* [Run WeeWX as a non-root user](Run-as-a-non-root-user-using-init)
* [Put log messages from WeeWX into a separate file](logging)
* [Use logwatch to report WeeWX log messages](logwatch)
* [Configure WeeWX to run under systemd](systemd)
* [Reduce the number of writes for SD cards or SSDs](Minimize-writes-on-SD-cards)
* [Configure a web server (Apache, NGINX or lighttpd)](Configure-a-web-server-(Apache,-NGINX-or-lighttpd))
* [Distinguish between USB devices using `udev` rules](udevrules)
* [Run WeeWX in Docker](docker)
* [Run WeeWX in a python virtual environment](Run-WeeWX-in-a-virtual-environment)

#### Data management
* [SQLite vs MySQL](SQLite-vs-MySQL)
* [Clean up old 'bad' data](Cleaning-up-old-'bad'-data)
* [Upload missing data to weather underground](http://www.weewx.com/wunderfixer/)
* [Switching to the new, extended schema `wview_extended`](Switching-to-the-new-wview_extended-schema)
* [Transfer from MySQL to sqlite](Transfer%20from%20MySQL%20to%20sqlite/)
* [Transfer from sqlite to MySQL](Transfer%20from%20sqlite%20to%20MySQL)
* [SQLBackup, an optional SLE to perform database backups](https://github.com/glennmckechnie/weewx-sqlbackup/wiki/SQLBackup-README) :heavy_check_mark:
* [Using the RSYNC skin as a backup solution](Using-the-RSYNC-skin-as-a-backup-solution)
* [Data Consolidation - changing sampling interval](Data-Consolidation---changing-sampling-interval)
* [Importing data from Weatherlink](Importing-data-from-Weatherlink)
* [Migrating from wview](Moving-the-wview-database)

#### Troubleshooting
* [Troubleshooting Davis stations](Troubleshooting-the-Davis-Vantage-station)
* [Deal with FineOffset USB lockup](FineOffset-USB-lockup)
* [Pillow fights](Pillow-fights)

#### Hardware
* [Use one-wire sensors with WeeWX](Onewire-sensors)
* [Get data from an Observer/WeatherSleuth/WeatherRanger](observer)
* [Get your Aercus WeatherSleuth (Observer) working with WeeWX](weathersleuth)
* [Run WeeWX on a Raspberry Pi](Raspberry-Pi)
* [Run WeeWX on a Raspberry Pi with a read-only file system : RorPi](https://github.com/glennmckechnie/rorpi-raspberrypi/wiki/Rorpi-Home)
* [Raspberry Pi weather station with i2C sensors](Raspberry-Pi-weather-station-with-i2C-sensors)
* [Get data from an Acurite bridge (smartHUB or aculink)](acuritebridge)
* [Run WeeWX on OpenWRT](weewx-on-openwrt)
* [Run WeeWX on OpenWRT on a travel router MT300N with Fine Offset WH1080](openwrt)
* [hostapd WiFi setup for Interceptor driver](hostapd-WiFi-setup-for-Interceptor-driver)
* [Diagnose hardware USB issues](usb-issues)
* [Use SDR on a Raspberry Pi with Acurite sensors](sdr-rpi-recipe)
* [Get data from ecowitt GW1000](gw1000-recipe)
* [Collect data from wifi sensors](https://hackaday.io/project/101680-solar-powered-wifi-temperature-sensor-for-weewx)
* [Get data from Acurite Atlas on a RPi](https://github.com/ChrisM510/AtlasWeeWX/wiki/Acurite-Atlas-on-WeeWX-RTL-SDR-RTL_433-RPi-(Page-1))
* [Building a Pi based PWS](https://github.com/David-Enst/WeeWX-BCRobotics)
* [Measure water/snow level using Maxbotix ultrasonic sensor](maxbotix-recipe)

### Software
* [How to calculate values in templates](calculate-in-templates)


### Extensions to WeeWX

Use extensions to add capabilities to a WeeWX installation.  Drivers enable WeeWX to collect data from various sources, services collect and emit data, skins contain the templates for generating reports, and uploaders transfer data to online services.


#### Drivers
* [airlink - Collect data from Davis AirLink air quality sensor](https://github.com/chaunceygardiner/weewx-airlink)
* [airmar - Collect data from Airmar 150WX](https://github.com/fullergalway/weewx-airmar-150wx-driver)
* [ambientweatherapi-json - Collect data from the Ambient Weather API without the need for any additional hardware](https://github.com/themoosman/weewx-ambientweatherapi-json)
* [atmocom - Collect data from Atmocom station](https://github.com/captain-coredump/weewx-atmocom-udp/)
* [aws - Collect data from an Arduino connected to a Davis anemometer](https://github.com/wrybread/weewx-ArduinoWeatherStation)
* [byows_rpi - Collect data from the Build Your Own Weather Station - Raspberry Pi](https://github.com/jardiamj/BYOWS_RPi)
* [cm1 - Collect data from Dyacon CM1 weather station interface](https://github.com/matthewwall/weewx-cm1)
* [columbia_ms - Collect data from Columbia Weather MicroServer supported sensors](https://github.com/bburton/weewx-columbia-ms) :heavy_check_mark:
* [envoy - Collect data from Enphase Envoy solar controller](https://github.com/matthewwall/weewx-envoy)
* [gw1000 - Collect data from Ecowitt GW1000/GW1100/GW2000](https://github.com/gjr80/weewx-gw1000) :heavy_check_mark:
* [hackulink - Collect data from AcuLink Internet Bridge via OpenWRT](http://geekfun.com/hackulink/)
* [Hp1000 - collect data from HP1000, (WS1001, XC0422 etc.) via WiFi connection](https://github.com/AussieSusan/HP1000) :heavy_check_mark:
* [id5001 - Collect data from Heathkit ID5001 with serial port option](https://github.com/n2ee/weewx-id5001)
* [interceptor - Collect data from Acurite Bridge, Fine Offset HP1000/WH2600, OS LW301/LW302, LaCross GW1000U, or any station that sends to WU](https://github.com/matthewwall/weewx-interceptor) :heavy_check_mark:
* [ip100 - Collect data from RainWise IP100](https://github.com/chaunceygardiner/weewx-ip100) :heavy_check_mark:
* [ipwx - Collect data from AcuLink Internet Bridge via Raspberry Pi](http://nincehelser.com/ipwx/)
* [klimalogg - Collect data from Klimalogg Pro](https://github.com/matthewwall/weewx-klimalogg) :heavy_check_mark:
* [mate - Collect data from Outback MATE3 solar/battery controller](https://github.com/matthewwall/weewx-mate)
* [maxbotix - Augment station data with data from maxbotix ultrasonic rangefinder](maxbotix) :heavy_check_mark:
* [meteostick - Collect data from Meteostick](https://github.com/matthewwall/weewx-meteostick) :heavy_check_mark:
* [MQTTSubscribe - Collect data from JSON, keyword, or individual topic MQTT messages](https://github.com/bellrichm/WeeWX-MQTTSubscribe) :heavy_check_mark:
* [netatmo - Collect data from netatmo](https://github.com/matthewwall/weewx-netatmo)
* [nm150 - Collect data from New Mountain NM150](https://github.com/matthewwall/weewx-nm150)
* [observerip - Collect data from ObserverIP (Fine Offset HP1000)](https://github.com/dkmcode/weewx-observerip)
* [owfs - Collect data from one-wire sensors](owfs) :heavy_check_mark:
* [purpleair - Collect data from PurpleAir particulate sensor](https://github.com/bakerkj/weewx-purpleair)  :heavy_check_mark:
* [rmyro - Collect data from RM Young ResponseOne (92000 only)](https://github.com/doubleohwhatever/weewx-rmyro)
* [rtldavis - Collect data from software-defined radio (SDR) for Davis Vantage stations](https://github.com/lheijst/weewx-rtldavis) :heavy_check_mark:
* [sdr - Collect data from software-defined radio (SDR) receiver](https://github.com/matthewwall/weewx-sdr) :heavy_check_mark:
* [sds011 - Collect data from SDS011 particulate sensor](https://github.com/matthewwall/weewx-sds011)
* [si1000 - Collect data from Si1000 radio receiver (for Davis stations)](DavisSi1000-radio)
* [socketlogger - Collect data from ObserverIP (Fine Offset HP1000) by intercepting traffic](http://obrienlabs.net/redirecting-weather-station-data-from-observerip/)
* [swb - Collect data from SMA Sunny WebBox](https://github.com/matthewwall/weewx-swb)
* [tempestWS - Collect data from the Weatherflow Tempest Websocket API](https://github.com/livysdad27/tempestWS)
* [tfrc - Collect data from software-defined radio using tfrec](https://github.com/matthewwall/weewx-tfrc) :heavy_check_mark:
* [tracer - Collect data from Tracer solar charge controllers](https://github.com/matthewwall/weewx-tracer)
* [twi - Collect data from Texas Weather Instruments hardware](https://github.com/matthewwall/weewx-twi) :heavy_check_mark:
* [uradmon - Collect data from a Uradmon Environmental Monitor](https://github.com/glennmckechnie/weewx-uradmon) :heavy_check_mark:
* [vedirect - Collect data from Victron MPPT solar/battery controller](https://github.com/matthewwall/weewx-vedirect)
* [w820 - Collect data from Ventus W820 bluetooth station](https://github.com/daduke/ventusw820)
* [wanet - Collect data from Wanet Wario ME11/12 meteo station](https://sourceforge.net/projects/wariome11)
* [weatherflow - Collect data from WeatherFlow hardware](https://github.com/captain-coredump/weatherflow-udp)
* [WeatherLink Live - Collect data from Davis WeatherLink Live using both polling and UDP broadcast API. Supports any combination of sensors and transmitters.](https://github.com/michael-slx/weewx-weatherlink-live) :heavy_check_mark:
* [WeatherLink Live JSON - Collect data from Davis WeatherLink Live via periodic polling](https://github.com/vinceskahan/weewx-weatherlinklive-json)
* [WeatherLink Live UDP - Collect data from Davis WeatherLink Live (with 2.5s UDP updates)](https://github.com/grebleem/weewx-weatherlinkliveudp) :heavy_check_mark:
* [WeatherLink Live - Collect data from Davis WeatherLink Live including archive from Weatherlink.com when data is lost.](https://github.com/Drealine/weatherlinklive-driver-weewx) :heavy_check_mark:
* [WeatherLink Live WLL - Collect data from Davis WeatherLink Live devices](https://github.com/jonotaegi/weewx-wll)
* [weewxMQTT - Collect data by subscribing to MQTT topics](https://github.com/morrowwm/weewxMQTT) :heavy_check_mark: 
* [wh23xx - Collect data from Fine Offset WH23xx (Tycon TP2700)](https://github.com/matthewwall/weewx-wh23xx)
* [wlink - Collect data from weatherlink.com](wlink)
* [wmII - Collect data from Davis Weather Monitor II Station](https://github.com/jardiamj/wmII): It should also work for Davis Wizzard and Perception stations but that hasn't been tested.
* [wmr89 - Collect data from the Oregon Scientific WMR89](https://github.com/tkeffer/weewx-wmr89). :heavy_check_mark:
* [ws6in1 - Collect data WS 6 in 1 weather stations with PC console](https://github.com/bobatchley/weewx-ws6in1) :heavy_check_mark:
* [wxt5x0 - Collect data from Vaisala WXT5x0 stations](https://github.com/matthewwall/weewx-wxt5x0)
* [BCRobotics - Collect data from a BC Robotics and Pi based PWS](https://github.com/David-Enst/WeeWX-BCRobotics). :heavy_check_mark:

#### Services
* [airQ - Retrieve air quality data from Air-Q device of Corant GmbH](https://github.com/roe-dl/weewx-airQ) (PM<sub>1.0</sub>, PM<sub>2.5</sub>, PM<sub>10.0</sub>, TVOC, CO, CO<sub>2</sub>, O<sub>2</sub>, O<sub>3</sub>, NO<sub>2</sub>, H<sub>2</sub>S, SO<sub>2</sub>, noise, health index, performance index)
* [aprs - Generate APRS-compliant weather packets](/cavedon/weewx-aprs)
* [aqi - Calculate Air Quality Index](https://github.com/jonathankoren/weewx-aqi)
* [as3935 - Augment station data with lightning strike data from AS3935 attached to RPi](as3935)
* [bme280wx - Use temp/pressure/humidity data from a BME280 attached to a Raspberry Pi](https://gitlab.com/wjcarpenter/bme280wx)
* [cmon - Monitor CPU, Memory, Disk, and Network on the computer running weewx](cmon)
* [crt - Emit a Cumulus realtime.txt file whenever sensor readings change](crt) :heavy_check_mark: 
* [csv - Emit loop or archive data to a file in CSV format](https://github.com/matthewwall/weewx-csv)
* [cwxn - Emit a Cumulus wxnow.txt file whenever sensor readings change](cwxn)
* [davishealthapi - Record and display Davis sensor health telemetry from the online API (signal strength, battery, etc.)](https://github.com/uajqq/weewx-davishealthapi)
* [DWD - forecast and warnings data for WeeWX and Belchertown skin, augment WeeWX data by readings from governmental weather stations](https://github.com/roe-dl/weewx-DWD) (possible data sources: DWD Germany, ZAMG Austria, and by the Open-Meteo API MeteoFrance, MSC Canada, NOAA U.S.A., ECMWF E.U., MET Norway, JMA Japan)
* [filepile - Add additional types to a WeeWX data stream via a file](https://github.com/tkeffer/filepile) :heavy_check_mark:
* [forecasting - Download, generate, and display forecasts and tides](forecasting)
* [knx - Send EIBnet/IP packets with weather information](knx)
* [luxtronik - Augment station data with data from Luxtronik heatpump controller](https://github.com/tsoodla/weewx-luxtronik) :heavy_check_mark:
* [maxbotix - Augment station data with data from maxbotix ultrasonic rangefinder](maxbotix) :heavy_check_mark:
* [MQTTSubscribe - Augment station data with JSON, keyword, or individual topic MQTT messages](https://github.com/bellrichm/WeeWX-MQTTSubscribe) :heavy_check_mark:
* [nmea-xdr - Augment data from a source of NMEA 0183 XDR sentences](https://github.com/tkeffer/weewx-nmea-xdr) :heavy_check_mark: 
* [opsgenie - Send heartbeats and alerts to cloud alerting service OpsGenie](opsgenie)
* [owfs - Augment station data with data from one-wire sensors](owfss) :heavy_check_mark:
* [photovoltaics - Retrieve live data from E3/DC S10 solar system, save to a database, output to MQTT, and present in skins](https://github.com/roe-dl/weewx-photovoltaics) :heavy_check_mark:
* [PiSenseHat - Add measure to loop data from PiSense Hat sensor set](https://github.com/sbsrouteur/weewx-PiSenseHat) :heavy_check_mark:
* [PrecipMeter - Receive, process and save data from disdrometers like Ott Parsivel<sup>2</sup> or Thies LNM or other devices that send data in ASCII format](https://github.com/roe-dl/weewx-precipmeter), includes precipitation intensity and kind (drizzle, rain snow, hail etc.), metorological optical range, radar reflectivity factor etc.)
* [sds011 - Add pm measure to loop data](https://github.com/sbsrouteur/weewx-sds011) \(forked from SDS011 driver updated for pyhton3\) :heavy_check_mark: 
* [snmp - Retrieve data by SNMP](https://github.com/roe-dl/weewx-snmp) 
  (for sensors that speak SNMP and for monitoring equipment like UPS) :heavy_check_mark:
* [sunduration - Augment station data with sunshine duration - for Davis Vantage Pro 2 with solar radiation sensor](https://github.com/Jterrettaz/sunduration) :heavy_check_mark: 
* [tristar - Augment station data with solar controller data from a Tristar Charge Controller](https://github.com/jim-olsen/weewx_tristar)
* [weewx-nws - NWS Weather Forecast and Alerts WeeWX Extention](https://github.com/chaunceygardiner/weewx-nws) :heavy_check_mark:
* [WXgraphic - Weather Graphic: A php driven generator to create a weather graphic image via your web server](https://github.com/glennmckechnie/weewx-WXgraphic) :heavy_check_mark:
* [wxobs - Provides a php driven report page to extract archival data as Daily Summaries](https://github.com/glennmckechnie/weewx-wxobs) :heavy_check_mark:
* [zabbix - Emit loop data to a zabbix server](https://github.com/RandomReaper/weewx-zabbix)


#### Skins
* [alltimeSeasons](https://github.com/glennmckechnie/alltimeSeasons) An addition to the Seasons skin that outputs some NOAA data as html (From Bootstrap via gedger)  :heavy_check_mark:
* [amphibian](amphibian)
* [Belchertown](https://github.com/poblabs/weewx-belchertown) (live weather feed) :heavy_check_mark:
* [bootstrap](Bootstrap) (live weather feed) :heavy_check_mark:
* [byteweather](https://byteinsight.co.uk/freebies/weewx-byte-weather-template/)
* [exfoliation](exfoliation)
* [Flat](http://projects.neoground.com/sofaskin) (sofaskin)
* [French Standard skin](weewx-french-standard-skin)
* [jas](https://github.com/bellrichm/weewx-jas) (A highly configurable skin using ECharts for charting, Bootstrap for layout, and MQTT for live updates.) :heavy_check_mark:
* [JSON](https://github.com/teeks99/weewx-json) (add json output to an existing skin) :heavy_check_mark:
* [MesoWX](https://github.com/glennmckechnie/weewx-mesowx) (live weather feed) :heavy_check_mark:
* [NeoWX](https://neoground.com/projects/neowx) :heavy_check_mark:
* [NeoWX Material](https://neoground.com/projects/neowx-material) :heavy_check_mark:
* [Niculskin](https://gitlab.com/ngulden/niculskin)
* [Responsive](responsive)
* [simple](simple)
* [SteelSeries Weather Gauges](steelseries) :heavy_check_mark:
* [SVG weather symbols and icons](https://github.com/roe-dl/weathericons) (not a complete skin but only the symbols and icons in Scalable Vector Graphics format)
* [Thème graphique standard de weewx en français](Th%C3%A8me-graphique-standard-de-weewx-en-fran%C3%A7ais-%7Bweewx-french-standard-skin-page-in-french%7D) (standard skin in french)
* [Washboard](https://gitlab.com/jquagga/Washboard)
* [WeeRT](https://github.com/tkeffer/weert-js) (experimental live weather feed using Node+React) :heavy_check_mark:
* [WeeWX Weather App](https://github.com/evilbunny2008/weeWXWeatherApp/wiki) (Android)
* [WeeWX-WD](weewx-wd) :heavy_check_mark:
* [weewx-wdc](https://github.com/Daveiano/weewx-wdc) (live weather feed) :heavy_check_mark:
* [weewx-Weather34](https://github.com/steepleian/weewx-Weather34)(real time weather feed, WeeWX port of the popular Weather34 template):heavy_check_mark: 

#### XType extensions
[XType extensions](xtypes) add new observation and aggregation types to WeeWX
* [weewx-aqi-xtype](https://github.com/bellrichm/weewx-aqi-xtype) - Calculate the AQI for a given air pollutant value.
* [weewx-xaggs](https://github.com/tkeffer/weewx-xaggs) - A grab-bag of new aggregation types, such as the historical highs or lows for a date. :heavy_check_mark:
* [weewx-GTS](https://github.com/roe-dl/weewx-GTS) - Provides "Grünlandtemperatursumme" (GTS), a kind of growing degree days, the date, when it exceeds the magic value of 200, the total solar energy received during some arbitrary timespan, dayET and ET24 as opposite to dayRain and rain24, absolute humidity, (saturation) vapor pressure, mixing ratio, (potential) equivalent temperature :heavy_check_mark:
* [weewx-phenology](http://lacusveris.com/Phenology) - Growing Degree-Days development models for various insect pests, showing when to apply control strategies to minimize crop damage:heavy_check_mark:

#### Generators
* [sftp - Upload data using SFTP (not FTPS)](https://github.com/matthewwall/weewx-sftp) :heavy_check_mark:
* [weewx-svg2png - Create thumbnail images, weather mini-pics, and webcam images with weather data by converting WeeWX-created SVG files to PNG](https://github.com/roe-dl/weewx-svg2png) :heavy_check_mark:

#### Uploaders
* <img src="http://weewx.com/consumers/emoncms.png" width='16'/> [emoncms](emoncms) :heavy_check_mark:
* <img src="http://graphite.readthedocs.org/favicon.ico" width='16'/> [graphite](https://github.com/ampledata/weewx_graphite)
* <img src="https://www.idokep.hu/images/icon/idokep_tab.png" width='16'/> [idokep](https://github.com/lorantkurthy/weewx-idokep/)
* <img src="http://weewx.com/consumers/influx-logo.png" width='16'/> [influxdb](https://github.com/matthewwall/weewx-influx) :heavy_check_mark:
* <img src="http://mastodon.help/favicon.ico" width='16'/> [mastodon](https://github.com/glennmckechnie/weewx-mastodon) :heavy_check_mark:
* <img src="https://meteo-services.com/images/weather365-logo-new.png" width='16'/> [Meteo-Services](Meteo-Services) :heavy_check_mark:
* <img src="http://meteotemplate.com/web/logoBlack.png" width='16'/> [meteotemplate](https://github.com/matthewwall/weewx-meteotemplate) :heavy_check_mark:
* <img src="http://mqtt.org/favicon.ico" width='16'/> [mqtt](mqtt) :heavy_check_mark:
* <img src="https://curl.se/favicon.ico" width='16'/> [notifier](https://github.com/teeks99/weewx-notify) :heavy_check_mark: 
* <img src="https://opensensemap.org/images/a86afe07.favicon.svg" width='16'> [OpenSenseMap](https://github.com/sbsrouteur/weewx-opensensemap) :heavy_check_mark:
* <img src="http://openweathermap.org/favicon.ico" width='16'/> [openweathermap](openweathermap) :heavy_check_mark:
* <img src="http://services.previmeteo.com/fr/assets/img/previmeteo/favicon-previmeteo.jpg" width='16'/> [previmeteo](https://github.com/previmeteo/weewx-previmeteo)
* <img src="https://prometheus.io/assets/favicons/favicon.ico" width='16'/> [prometheus](https://github.com/sulrich/weewx-prompush)
* <img src="https://avatars3.githubusercontent.com/u/739550?v=3&s=200" width='16'/> [pusher](https://github.com/raymondelooff/weewx-pusher)
* <img src="https://rainmachine.com/favicon.ico" width='16'/> [RainMachine](RainMachine)
* <img src="http://www.regionalwetter-sa.de/favicon.ico" width=16 /> [Regionalwetter Sachsen-Anhalt](https://github.com/roe-dl/weewx-rwsa) :heavy_check_mark:
* <img src="https://www.infoclimat.fr/images/footer_IClogo.png" width='16'/> [Réseau Static de www.infoclimat.fr](https://github.com/AssociationInfoclimat/StatIC-WeeWX)
* <img src="https://rp5.ru/favicon.ico" width='16'/> [RP5](https://github.com/sapegin-o1eg/weewx-rp5)
* <img src="http://weewx.com/consumers/seg.ico" width='16'/> [smart energy groups](seg)
* <img src="http://thingspeak.com/favicon.ico" width='16'/> [thingspeak](thingspeak) :heavy_check_mark:
* <img src="http://twitter.com/favicon.ico" width='16'/> [twitter](twitter) :heavy_check_mark:
* <img src="http://weatherbug.com/favicon.ico" width='16'/> [weatherbug](weatherbug) :heavy_check_mark:
* <img src="http://weewx.com/consumers/weathercloud.ico" width='16'/> [weathercloud](https://github.com/matthewwall/weewx-wcloud) :heavy_check_mark:
* <img src="http://wetter.com/favicon.ico" width='16'/> [wetter](wetter) :heavy_check_mark:
* <img src="http://www.zschorlau-wetter.de/pics/favicon.ico" width=16 /> [Wetternetz Sachsen](https://github.com/roe-dl/weewx-wns) :heavy_check_mark:
* <img src="http://windfinder.com/favicon.ico" width='16'/> [windfinder](windfinder) :heavy_check_mark:
* <img src="https://stations.windguru.cz/img/apple-touch-icon-precomposed.png" width='16'/> [windguru](https://github.com/claudobahn/weewx-windguru) :heavy_check_mark:
* <img src="http://weewx.com/consumers/windy.png" width='16'/> [windy](https://github.com/matthewwall/weewx-windy) :heavy_check_mark:
* <img src="http://weewx.com/consumers/xively.ico" width='16'/> [xively/cosm/pachube](xively)

### Development notes
* [v5 design notes](Version-5)
* [v5 platform packages](Version-5-packages)
* [Development recipes](https://raw.githubusercontent.com/weewx/weewx/master/DEV_NOTES.txt)
* [Development and testing infrastructure](devtest-infrastructure)
* [Dependency matrix](weewx4-compatibility-matrix)
* [Weewx Driver Development Tips](Weewx-Driver-Development-Tips)
* [Supporting Python 3](Python-3-strategy)
* [Proposal for logging events in V4](WeeWX-v4-and-logging)
* [Proposal for user-defined types in V4 (xtypes)](xtypes)
* [Tags for series (V4.5 or later)](Tags-for-series)
* [Accumulators](Accumulators)
* [Registry](registry)
* [Storing text in the database](Storing-text-in-the-database)
* [Installer options](installer-options)
* [Character encodings](Character-encodings)
