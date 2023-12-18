## Welcome to the WeeWX wiki!

This is the place to go for information about the many extensions available for WeeWX, as well as random "how to" tips.  For information on how to download, install, and configure WeeWX, see the [WeeWX web site](http://www.weewx.com/).

Edit this page as you see fit; this wiki uses [Markdown](https://help.github.com/articles/github-flavored-markdown/) syntax, not HTML.  To add a new page, reference it within brackets, e.g.: [SamplePage].

:heavy_check_mark: = Tested with weewx4 and python3

### General
* [Answers to frequently-asked questions (FAQ)](WeeWX-Frequently-Asked-Questions)
* [Posting to weewx-user](Help!-Posting-to-weewx-user)
* [How to view the log](view-logs)
* [Informationen auf Deutsch](https://www.woellsdorf-wetter.de/software/weewx.html)

### Basic principles
* [What you should know about paths](Understanding-paths)
* [What you should know about permissions](Understanding-permissions)
* [What is the difference between `barometer`, `pressure`, and `altimeter`?](Barometer,-pressure,-and-altimeter)
* [What is `wind gust` and how is it different from `maximum wind speed`?](windgust)
* [Wind chill and heat indexes](Wind-chill-and-heat-index)
* [Watts/m&sup2; vs lux](Watts-and-lux)
* [Strategies for getting data into WeeWX](intake-strategies)
* [Strategies for publishing dashboards](dashboards)

### Hardware recommendations
* [On what hardware do you run WeeWX?](hardware)
* [Comparison of weather station hardware](http://weewx.com/hwcmp.html)
* [Details about Fine Offset clones from wxforum](https://www.wxforum.net/index.php?topic=40730.0)
* [Switched USB hubs](usb-hubs)

### Guides (How to ...)

#### WeeWX configuration
* [Manage extensions](extensions)
* [Change from one driver to another](Change-driver)
* [Run multiple instances of WeeWX on one computer](weewx-multi)
* [Convert from setup.py to deb](How%20to%20convert%20from%20setup.py%20install%20to%20debian%20install)
* [Migrating `setup.py` installs to Version 5](v5-upgrade)
* [Implement a multi-threaded service](multi-threaded-service)
* [Colorize the UV index](Colored%20UV%20index)
* [How to calculate values in templates](calculate-in-templates)

#### Operating system configuration
* [Time services](Time-services)
* [Run WeeWX as a non-root user](Run-as-a-non-root-user-using-init)
* [Put log messages from WeeWX into a separate file](logging)
* [Use logwatch to report WeeWX log messages](logwatch)
* [Reduce the number of writes for SD cards or SSDs](Minimize-writes-on-SD-cards)
* [Configure a web server (Apache, NGINX or lighttpd)](Configure-a-web-server-(Apache,-NGINX-or-lighttpd))
* [Distinguish between USB devices using `udev` rules](udevrules)
* [Run WeeWX in Docker](docker)
* [Run WeeWX in a python virtual environment](Run-WeeWX-in-a-virtual-environment)
* [Configure WeeWX to run under systemd](systemd)

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
* [Troubleshooting pip installs](pip-troubleshooting)

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


### Extensions to WeeWX

Use extensions to add capabilities to a WeeWX installation.  Drivers enable WeeWX to collect data from various sources, services collect and emit data, skins contain the templates for generating reports, [XType extensions](xtypes) add new observation and aggregation types, and uploaders transfer data to online services.


#### Drivers
* [airlink](https://github.com/chaunceygardiner/weewx-airlink) - Collect data from Davis AirLink air quality sensor
* [airmar](https://github.com/fullergalway/weewx-airmar-150wx-driver) - Collect data from Airmar 150WX
* [ambientweatherapi-json](https://github.com/themoosman/weewx-ambientweatherapi-json) - Collect data from the Ambient Weather API without the need for any additional hardware
* [atmocom](https://github.com/captain-coredump/weewx-atmocom-udp/) - Collect data from Atmocom station
* [aws](https://github.com/wrybread/weewx-ArduinoWeatherStation) - Collect data from an Arduino connected to a Davis anemometer
* [BCRobotics](https://github.com/David-Enst/WeeWX-BCRobotics) - Collect data from a BC Robotics and Pi based PWS :heavy_check_mark:
* [byows_rpi](https://github.com/jardiamj/BYOWS_RPi) - Collect data from the Build Your Own Weather Station - Raspberry Pi
* [cm1](https://github.com/matthewwall/weewx-cm1) - Collect data from Dyacon CM1 weather station interface
* [columbia_ms](https://github.com/bburton/weewx-columbia-ms) - Collect data from Columbia Weather MicroServer supported sensors :heavy_check_mark:
* [envoy](https://github.com/matthewwall/weewx-envoy) - Collect data from Enphase Envoy solar controller
* [gw1000](https://github.com/gjr80/weewx-gw1000) - Collect data from Ecowitt GW1000/GW1100/GW2000 :heavy_check_mark:
* [hackulink](http://geekfun.com/hackulink/) - Collect data from AcuLink Internet Bridge via OpenWRT
* [Hp1000](https://github.com/AussieSusan/HP1000) - collect data from HP1000, (WS1001, XC0422 etc.) via WiFi connection :heavy_check_mark:
* [id5001](https://github.com/n2ee/weewx-id5001) - Collect data from Heathkit ID5001 with serial port option
* [interceptor](https://github.com/matthewwall/weewx-interceptor) - Collect data from Acurite Bridge, Fine Offset HP1000/WH2600, OS LW301/LW302, LaCross GW1000U, or any station that sends to WU
* [ip100](https://github.com/chaunceygardiner/weewx-ip100) - Collect data from RainWise IP100 :heavy_check_mark:
* [ipwx](http://nincehelser.com/ipwx/) - Collect data from AcuLink Internet Bridge via Raspberry Pi
* [klimalogg](https://github.com/matthewwall/weewx-klimalogg) - Collect data from Klimalogg Pro :heavy_check_mark:
* [mate](https://github.com/matthewwall/weewx-mate) - Collect data from Outback MATE3 solar/battery controller
* [maxbotix](maxbotix) - Augment station data with data from maxbotix ultrasonic rangefinder :heavy_check_mark:
* [meteostick](https://github.com/matthewwall/weewx-meteostick) - Collect data from Meteostick :heavy_check_mark:
* [MQTTSubscribe](https://github.com/bellrichm/WeeWX-MQTTSubscribe) - Collect data from JSON, keyword, or individual topic MQTT messages :heavy_check_mark:
* [netatmo](https://github.com/matthewwall/weewx-netatmo) - Collect data from netatmo
* [nm150](https://github.com/matthewwall/weewx-nm150) - Collect data from New Mountain NM150
* [observerip](https://github.com/dkmcode/weewx-observerip) - Collect data from ObserverIP (Fine Offset HP1000)
* [owfs](owfs) - Collect data from one-wire sensors :heavy_check_mark:
* [purpleair](https://github.com/bakerkj/weewx-purpleair) - Collect data from PurpleAir particulate sensor :heavy_check_mark:
* [rmyro](https://github.com/doubleohwhatever/weewx-rmyro) - Collect data from RM Young ResponseOne (92000 only)
* [rtldavis](https://github.com/lheijst/weewx-rtldavis) - Collect data from software-defined radio (SDR) for Davis Vantage stations :heavy_check_mark:
* [sdr](https://github.com/matthewwall/weewx-sdr) - Collect data from software-defined radio (SDR) receiver :heavy_check_mark:
* [sds011](https://github.com/matthewwall/weewx-sds011) - Collect data from SDS011 particulate sensor
* [si1000](DavisSi1000-radio) - Collect data from Si1000 radio receiver (for Davis stations)
* [socketlogger](http://obrienlabs.net/redirecting-weather-station-data-from-observerip/) - Collect data from ObserverIP (Fine Offset HP1000) by intercepting traffic
* [swb](https://github.com/matthewwall/weewx-swb) - Collect data from SMA Sunny WebBox
* [tempestWS](https://github.com/livysdad27/tempestWS) - Collect data from the Weatherflow Tempest Websocket API
* [tfrc](https://github.com/matthewwall/weewx-tfrc) - Collect data from software-defined radio using tfrec :heavy_check_mark:
* [tracer](https://github.com/matthewwall/weewx-tracer) - Collect data from Tracer solar charge controllers
* [twi](https://github.com/matthewwall/weewx-twi) - Collect data from Texas Weather Instruments hardware :heavy_check_mark:
* [uradmon](https://github.com/glennmckechnie/weewx-uradmon) - Collect data from a Uradmon Environmental Monitor :heavy_check_mark:
* [vedirect](https://github.com/matthewwall/weewx-vedirect) - Collect data from Victron MPPT solar/battery controller
* [w820](https://github.com/daduke/ventusw820) - Collect data from Ventus W820 bluetooth station
* [wanet](https://sourceforge.net/projects/wariome11) - Collect data from Wanet Wario ME11/12 meteo station
* [weatherflow](https://github.com/captain-coredump/weatherflow-udp) - Collect data from WeatherFlow hardware
* [WeatherLink Live](https://github.com/michael-slx/weewx-weatherlink-live) - Collect data from Davis WeatherLink Live using both polling and UDP broadcast API :heavy_check_mark:
* [WeatherLink Live JSON](https://github.com/vinceskahan/weewx-weatherlinklive-json) - Collect data from Davis WeatherLink Live via periodic polling
* [WeatherLink Live UDP](https://github.com/grebleem/weewx-weatherlinkliveudp) - Collect data from Davis WeatherLink Live (with 2.5s UDP updates) :heavy_check_mark:
* [WeatherLink Live](https://github.com/Drealine/weatherlinklive-driver-weewx) - Collect data from Davis WeatherLink Live (including Weatherlink.com) when data is lost :heavy_check_mark:
* [WeatherLink Live WLL](https://github.com/jonotaegi/weewx-wll) - Collect data from Davis WeatherLink Live devices
* [weewxMQTT](https://github.com/morrowwm/weewxMQTT) - Collect data by subscribing to MQTT topics :heavy_check_mark: 
* [wh23xx](https://github.com/matthewwall/weewx-wh23xx) - Collect data from Fine Offset WH23xx (Tycon TP2700)
* [wlink](wlink) - Collect data from weatherlink.com
* [wmII](https://github.com/jardiamj/wmII) - Collect data from Davis Weather Monitor II, Wizzard, Perception
* [wmr89](https://github.com/tkeffer/weewx-wmr89) - Collect data from the Oregon Scientific WMR89 :heavy_check_mark:
* [ws6in1](https://github.com/bobatchley/weewx-ws6in1) - Collect data WS 6 in 1 weather stations with PC console :heavy_check_mark:
* [wxt5x0](https://github.com/matthewwall/weewx-wxt5x0) - Collect data from Vaisala WXT5x0 stations

#### Services
* [airQ](https://github.com/roe-dl/weewx-airQ) - Retrieve air quality data from Air-Q device of Corant GmbH (PM<sub>1.0</sub>, PM<sub>2.5</sub>, PM<sub>10.0</sub>, TVOC, CO, CO<sub>2</sub>, O<sub>2</sub>, O<sub>3</sub>, NO<sub>2</sub>, H<sub>2</sub>S, SO<sub>2</sub>, noise, health index, performance index)
* [aprs](/cavedon/weewx-aprs) - Generate APRS-compliant weather packets
* [aqi](https://github.com/jonathankoren/weewx-aqi) - Calculate Air Quality Index
* [as3935](as3935) - Augment station data with lightning strike data from AS3935 attached to RPi
* [bme280wx](https://gitlab.com/wjcarpenter/bme280wx) - Use temp/pressure/humidity data from a BME280 attached to a Raspberry Pi
* [cmon](cmon) - Monitor CPU, Memory, Disk, and Network on the computer running weewx
* [crt](crt) - Emit a Cumulus realtime.txt file whenever sensor readings change :heavy_check_mark: 
* [csv](https://github.com/matthewwall/weewx-csv) - Emit loop or archive data to a file in CSV format
* [cwxn](cwxn) - Emit a Cumulus wxnow.txt file whenever sensor readings change
* [davishealthapi](https://github.com/uajqq/weewx-davishealthapi) - Record and display Davis sensor health telemetry from the online API (signal strength, battery, etc.)
* [DWD](https://github.com/roe-dl/weewx-DWD) - forecast and warnings data for WeeWX and Belchertown skin, augment WeeWX data by readings from governmental weather stations (possible data sources: DWD Germany, ZAMG Austria, and by the Open-Meteo API MeteoFrance, MSC Canada, NOAA U.S.A., ECMWF E.U., MET Norway, JMA Japan)
* [filepile](https://github.com/tkeffer/filepile) - Add additional types to a WeeWX data stream via a file :heavy_check_mark:
* [forecasting](forecasting) - Download, generate, and display forecasts and tides
* [knx](knx) - Send EIBnet/IP packets with weather information
* [luxtronik](https://github.com/tsoodla/weewx-luxtronik) - Augment station data with data from Luxtronik heatpump controller :heavy_check_mark:
* [maxbotix](maxbotix) - Augment station data with data from maxbotix ultrasonic rangefinder :heavy_check_mark:
* [MQTTSubscribe](https://github.com/bellrichm/WeeWX-MQTTSubscribe) - Augment station data with JSON, keyword, or individual topic MQTT messages :heavy_check_mark:
* [nmea-xdr](https://github.com/tkeffer/weewx-nmea-xdr) - Augment data from a source of NMEA 0183 XDR sentences :heavy_check_mark: 
* [opsgenie](opsgenie) - Send heartbeats and alerts to cloud alerting service OpsGenie
* [owfs](owfss) - Augment station data with data from one-wire sensors :heavy_check_mark:
* [photovoltaics](https://github.com/roe-dl/weewx-photovoltaics) - Retrieve live data from E3/DC S10 solar system, save to a database, output to MQTT, and present in skins :heavy_check_mark:
* [PiSenseHat](https://github.com/sbsrouteur/weewx-PiSenseHat) - Add measure to loop data from PiSense Hat sensor set :heavy_check_mark:
* [PrecipMeter](https://github.com/roe-dl/weewx-precipmeter) - Receive, process and save data from disdrometers like Ott Parsivel<sup>2</sup> or Thies LNM or other devices that send data in ASCII format, includes precipitation intensity and kind (drizzle, rain snow, hail etc.), metorological optical range, radar reflectivity factor, etc.
* [sds011](https://github.com/sbsrouteur/weewx-sds011) - Add pm measure to loop data (forked from SDS011 driver updated for python3) :heavy_check_mark: 
* [snmp](https://github.com/roe-dl/weewx-snmp)  - Retrieve data by SNMP (for sensors that speak SNMP and for monitoring equipment like UPS) :heavy_check_mark:
* [sunduration](https://github.com/Jterrettaz/sunduration) - Augment station data with sunshine duration - for Davis Vantage Pro 2 with solar radiation sensor :heavy_check_mark: 
* [tristar](https://github.com/jim-olsen/weewx_tristar) - Augment station data with solar controller data from a Tristar Charge Controller
* [weewx-nws](https://github.com/chaunceygardiner/weewx-nws) - NWS Weather Forecast and Alerts WeeWX Extention :heavy_check_mark:
* [WXgraphic](https://github.com/glennmckechnie/weewx-WXgraphic) - Weather Graphic: A php driven generator to create a weather graphic image via your web server :heavy_check_mark:
* [wxobs](https://github.com/glennmckechnie/weewx-wxobs) - Provides a php driven report page to extract archival data as Daily Summaries :heavy_check_mark:
* [zabbix](https://github.com/RandomReaper/weewx-zabbix) - Emit loop data to a zabbix server


#### Skins
* [alltimeSeasons](https://github.com/glennmckechnie/alltimeSeasons) - An addition to the Seasons skin that outputs some NOAA data as html (From Bootstrap via gedger)  :heavy_check_mark:
* [amphibian](amphibian)
* [Belchertown](https://github.com/poblabs/weewx-belchertown) - live weather feed :heavy_check_mark:
* [bootstrap](Bootstrap) - live weather feed :heavy_check_mark:
* [byteweather](https://byteinsight.co.uk/freebies/weewx-byte-weather-template/)
* [exfoliation](exfoliation)
* [Flat](http://projects.neoground.com/sofaskin) (sofaskin)
* [French Standard skin](weewx-french-standard-skin)
* [jas](https://github.com/bellrichm/weewx-jas) - A highly configurable skin using ECharts for charting, Bootstrap for layout, and MQTT for live updates :heavy_check_mark:
* [JSON](https://github.com/teeks99/weewx-json) - add json output to an existing skin :heavy_check_mark:
* [MesoWX](https://github.com/glennmckechnie/weewx-mesowx) - live weather feed :heavy_check_mark:
* [NeoWX](https://neoground.com/projects/neowx) :heavy_check_mark:
* [NeoWX Material](https://neoground.com/projects/neowx-material) :heavy_check_mark:
* [Niculskin](https://gitlab.com/ngulden/niculskin)
* [Responsive](responsive)
* [simple](simple)
* [SteelSeries Weather Gauges](steelseries) :heavy_check_mark:
* [SVG weather symbols and icons](https://github.com/roe-dl/weathericons) - not a complete skin but only the symbols and icons in Scalable Vector Graphics format
* [Thème graphique standard de weewx en français](Th%C3%A8me-graphique-standard-de-weewx-en-fran%C3%A7ais-%7Bweewx-french-standard-skin-page-in-french%7D) - standard skin in french
* [Washboard](https://gitlab.com/jquagga/Washboard)
* [WeeRT](https://github.com/tkeffer/weert-js) - experimental live weather feed using Node+React :heavy_check_mark:
* [WeeWX Weather App](https://github.com/evilbunny2008/weeWXWeatherApp/wiki) - Android
* [WeeWX-WD](weewx-wd) :heavy_check_mark:
* [weewx-wdc](https://github.com/Daveiano/weewx-wdc) - live weather feed :heavy_check_mark:
* [weewx-Weather34](https://github.com/steepleian/weewx-Weather34) - real time weather feed, WeeWX port of the popular Weather34 template :heavy_check_mark: 

#### XType extensions
* [weewx-aqi-xtype](https://github.com/bellrichm/weewx-aqi-xtype) - Calculate the AQI for a given air pollutant value.
* [weewx-xaggs](https://github.com/tkeffer/weewx-xaggs) - A grab-bag of new aggregation types, such as the historical highs or lows for a date. :heavy_check_mark:
* [weewx-GTS](https://github.com/roe-dl/weewx-GTS) - Provides "Grünlandtemperatursumme" (GTS), a kind of growing degree days, the date, when it exceeds the magic value of 200, the total solar energy received during some arbitrary timespan, dayET and ET24 as opposite to dayRain and rain24, absolute humidity, (saturation) vapor pressure, mixing ratio, (potential) equivalent temperature :heavy_check_mark:
* [weewx-phenology](http://lacusveris.com/Phenology) - Growing Degree-Days development models for various insect pests, showing when to apply control strategies to minimize crop damage:heavy_check_mark:

#### Generators
* [sftp](https://github.com/matthewwall/weewx-sftp) - Upload data using SFTP (not FTPS) :heavy_check_mark:
* [weewx-svg2png](https://github.com/roe-dl/weewx-svg2png) - Create thumbnail images, weather mini-pics, and webcam images with weather data by converting WeeWX-created SVG files to PNG :heavy_check_mark:

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
* [Proposal for logging (V4 or later)](WeeWX-v4-and-logging)
* [Proposal for user-defined types (xtypes)](xtypes)
* [Tags for series (V4.5 or later)](Tags-for-series)
* [Accumulators](Accumulators)
* [Registry](registry)
* [Storing text in the database](Storing-text-in-the-database)
* [Installer options](installer-options)
* [Character encodings](Character-encodings)
* [Benchmarks of file and image generation](Benchmarks-of-file-and-image-generation)
