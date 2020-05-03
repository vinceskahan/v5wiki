## Welcome to the WeeWX wiki!

This is the place to go for information about the many extensions available for WeeWX, as well as random "how to" tips.  For information on how to download, install, and configure WeeWX, see the [WeeWX web site](http://www.weewx.com/).

Edit this page as you see fit; this wiki uses [Markdown](https://help.github.com/articles/github-flavored-markdown/) syntax, not HTML.  To add a new page, reference it within brackets, e.g.: [SamplePage].

### Getting help
* [Help! Posting to weewx-user](https://github.com/weewx/weewx/wiki/Help!-Posting-to-weewx-user)

### Hardware recommendations
* [On what hardware do you run WeeWX?](hardware)
* [Comparison of weather station hardware](http://weewx.com/hwcmp.html)
* [Switched USB hubs](usb-hubs)

### HOWTO Pages

#### Basic principles
* [Beta-testing Weewx version 4](Beta-testing-Weewx-version-4)
* [What you should know about paths](Understanding-paths)
* [What is the difference between `barometer`, `pressure`, and `altimeter`?](Barometer,-pressure,-and-altimeter)
* [What is `wind gust` and how is it different from `maximum wind speed`?](windgust)
* [Wind chill and heat indexes](Wind-chill-and-heat-index)
* [Watts/m&sup2; vs lux](Watts-and-lux)
* [Strategies for getting data into WeeWX](intake-strategies)
* [Strategies for publishing dashboards](dashboards)

#### WeeWX configuration
* [Manage extensions](extensions)
* [Change from one driver to another](Change-driver)
* [Convert from setup.py to deb](How%20to%20convert%20from%20setup.py%20install%20to%20debian%20install)
* [Colorize the UV index](Colored%20UV%20index)
* [Run multiple instances of WeeWX on one computer](weewx-multi)
* [Implement a multi-threaded service](multi-threaded-service)

#### Operating system configuration
* [Run WeeWX as a non-root user](Run-as-a-non-root-user)
* [Put log messages from WeeWX into a separate file](logging)
* [Use logwatch to report WeeWX log messages](logwatch)
* [Configure WeeWX to run under systemd](systemd)
* [Reduce the number of writes for SD cards or SSDs](Minimize-writes-on-SD-cards)
* [Troubleshoot Pillow](Troubleshooting-Pillow)
* [Configure web server `apache`, `nginx`, or `lighttpd`](webserver)
* [Distinguish between USB devices using `udev` rules](udevrules)
* [Run WeeWX in Docker](docker)
* [Run WeeWX on Windows](weewx-on-windows)

#### Data management
* [SQLite vs MySQL](SQLite-vs-MySQL)
* [Clean up old 'bad' data](Cleaning-up-old-'bad'-data)
* [Upload missing data to weather underground](http://www.weewx.com/wunderfixer/)
* [Transfer from MySQL to sqlite](Transfer%20from%20MySQL%20to%20sqlite/)
* [Transfer from sqlite to MySQL](Transfer%20from%20sqlite%20to%20MySQL)
* [SQLBackup, an optional SLE to perform database backups](https://github.com/glennmckechnie/weewx-sqlbackup/wiki/SQLBackup-README)
* [Using the RSYNC skin as a backup solution](Using-the-RSYNC-skin-as-a-backup-solution)
* [Data Consolidation - changing sampling interval](Data-Consolidation---changing-sampling-interval)
* [Importing data from Weatherlink](Importing-data-from-Weatherlink)

#### Troubleshooting
* [Troubleshooting Davis stations](Troubleshooting-the-Davis-Vantage-station)
* [Deal with FineOffset USB lockup](FineOffset-USB-lockup)

#### Hardware
* [Add a sensor to any weather station](add-sensor)
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

### Extensions to WeeWX

Use extensions to add capabilities to a WeeWX installation.  Drivers enable WeeWX to collect data from various sources, services collect and emit data, skins contain the templates for generating reports, and uploaders transfer data to online services.

#### Drivers
* [airmar - Collect data from Airmar 150WX](https://github.com/fullergalway/weewx-airmar-150wx-driver)
* [atmocom - Collect data from Atmocom station](https://github.com/captain-coredump/weewx-atmocom-udp/)
* [aws - Collect data from an Arduino connected to a Davis anemometer](https://github.com/wrybread/weewx-ArduinoWeatherStation)
* [byows_rpi - Collect data from the Build Your Own Weather Station - Raspberry Pi](https://github.com/jardiamj/BYOWS_RPi)
* [cm1 - Collect data from Dyacon CM1 weather station interface](https://github.com/matthewwall/weewx-cm1)
* [envoy - Collect data from Enphase Envoy solar controller](https://github.com/matthewwall/weewx-envoy)
* [hackulink - Collect data from AcuLink Internet Bridge via OpenWRT](http://geekfun.com/hackulink/)
* [Hp1000 - collect data from HP1000, (WS1001, XC0422 etc.) via WiFi connection](https://github.com/AussieSusan/HP1000)
* [id5001 - Collect data from Heathkit ID5001 with serial port option](https://github.com/n2ee/weewx-id5001)
* [ip100 - Collect data from RainWise IP100](https://github.com/matthewwall/weewx-ip100)
* [interceptor - Collect data from Acurite Bridge, Fine Offset HP1000/WH2600, OS LW301/LW302, LaCross GW1000U, or any station that sends to WU](https://github.com/matthewwall/weewx-interceptor)
* [ipwx - Collect data from AcuLink Internet Bridge via Raspberry Pi](http://nincehelser.com/ipwx/)
* [klimalogg - Collect data from Klimalogg Pro](https://github.com/matthewwall/weewx-klimalogg)
* [mate - Collect data from Outback MATE3 solar/battery controller](https://github.com/matthewwall/weewx-mate)
* [meteostick - Collect data from Meteostick](https://github.com/matthewwall/weewx-meteostick)
* [MQTTSubscribe - Collect data from JSON, keyword, or individual topic MQTT messages](https://github.com/bellrichm/WeeWX-MQTTSubscribe)
* [netatmo - Collect data from netatmo](https://github.com/matthewwall/weewx-netatmo)
* [nm150 - Collect data from New Mountain NM150](https://github.com/matthewwall/weewx-nm150)
* [observerip - Collect data from ObserverIP (Fine Offset HP1000)](https://github.com/dkmcode/weewx-observerip)
* [owfs - Collect data from one-wire sensors](owfs)
* [purpleair - Collect data from PurpleAir particulate sensor](https://github.com/bakerkj/weewx-purpleair)
* [rmyro - Collect data from RM Young ResponseOne (92000 only)](https://github.com/doubleohwhatever/weewx-rmyro)
* [rtldavis - Collect data from software-defined radio (SDR) for Davis Vantage stations](https://github.com/lheijst/weewx-rtldavis)
* [sdr - Collect data from software-defined radio (SDR) receiver](https://github.com/matthewwall/weewx-sdr)
* [sds011 - Collect data from SDS011 particulate sensor](https://github.com/matthewwall/weewx-sds011)
* [si1000 - Collect data from Si1000 radio receiver (for Davis stations)](DavisSi1000-radio)
* [socketlogger - Collect data from ObserverIP (Fine Offset HP1000) by intercepting traffic](http://obrienlabs.net/redirecting-weather-station-data-from-observerip/)
* [swb - Collect data from SMA Sunny WebBox](https://github.com/matthewwall/weewx-swb)
* [tfrc - Collect data from software-defined radio using tfrec](https://github.com/matthewwall/weewx-tfrc)
* [tracer - Collect data from Tracer solar charge controllers](https://github.com/matthewwall/weewx-tracer)
* [twi - Collect data from Texas Weather Instruments hardware](https://github.com/matthewwall/weewx-twi)
* [uradmon - Collect data from a Uradmon Environmental Monitor](https://github.com/glennmckechnie/weewx-uradmon)
* [vedirect - Collect data from Victron MPPT solar/battery controller](https://github.com/matthewwall/weewx-vedirect)
* [w820 - Collect data from Ventus W820 bluetooth station](https://github.com/daduke/ventusw820)
* [wanet - Collect data from Wanet Wario ME11/12 meteo station](https://sourceforge.net/projects/wariome11)
* [weatherflow - Collect data from WeatherFlow hardware](https://github.com/captain-coredump/weatherflow-udp)
* [WeatherLinkLiveJSON - Collect data from Davis WeatherLink Live via periodic polling](https://github.com/vinceskahan/weewx-weatherlinklive-json)
* [wll - Collect data from Davis WeatherLink Live devices](https://github.com/jonotaegi/weewx-wll)
* [wh23xx - Collect data from Fine Offset WH23xx (Tycon TP2700)](https://github.com/matthewwall/weewx-wh23xx)
* [wlink - Collect data from weatherlink.com](wlink)
* [wmII - Collect data from Davis Weather Monitor II Station](https://github.com/jardiamj/wmII): It should also work for Davis Wizzard and Perception stations but that hasn't been tested.
* [weewxMQTT - Collect data by subscribing to MQTT topics](https://github.com/morrowwm/weewxMQTT)
* [ws6in1 - Collect data WS 6 in 1 weather stations with PC console](https://github.com/bobatchley/weewx-ws6in1)
* [wxt5x0 - Collect data from Vaisala WXT5x0 stations](https://github.com/matthewwall/weewx-wxt5x0)

#### Services
* [aprs - Generate APRS-compliant weather packets](/cavedon/weewx-aprs)
* [aqi - Calculate Air Quality Index](https://github.com/jonathankoren/weewx-aqi)
* [as3935 - Augment station data with lightning strike data from AS3935 attached to RPi](as3935)
* [bme280wx - Use temp/pressure/humidity data from a BME280 attached to a Raspberry Pi](https://gitlab.com/wjcarpenter/bme280wx)
* [cmon - Monitor CPU, Memory, Disk, and Network on the computer running weewx](cmon)
* [crt - Emit a Cumulus realtime.txt file whenever sensor readings change](crt)
* [csv - Emit loop or archive data to a file in CSV format](csv)
* [cwxn - Emit a Cumulus wxnow.txt file whenever sensor readings change](cwxn)
* [filepile - Add additional types to a WeeWX data stream via a file](https://github.com/tkeffer/filepile)
* [forecasting - Download, generate, and display forecasts and tides](forecasting)
* [maxbotix - Augment station data with data from maxbotix ultrasonic rangefinder](maxbotix)
* [MQTTSubscribe - Augment station data with JSON, keyword, or individual topic MQTT messages](https://github.com/bellrichm/WeeWX-MQTTSubscribe)
* [nmea-xdr - Augment data from a source of NMEA 0183 XDR sentences](https://github.com/tkeffer/weewx-nmea-xdr)
* [owfs - Augment station data with data from one-wire sensors](owfss)
* [opsgenie - Send heartbeats and alerts to cloud alerting service OpsGenie](opsgenie)
* [tristar - Augment station data with solar controller data from a Tristar Charge Controller](https://github.com/jim-olsen/weewx_tristar)
* [wxobs - Provides a php driven report page to extract archival data as Daily Summaries](https://github.com/glennmckechnie/weewx-wxobs)
* [zabbix - Emit loop data to a zabbix server](https://github.com/RandomReaper/weewx-zabbix)
* [luxtronik - Augment station data with data from Luxtronik heatpump controller
](https://github.com/tsoodla/weewx-luxtronik)

#### Skins
* [amphibian](amphibian)
* [Belchertown](https://github.com/poblabs/weewx-belchertown) (live weather feed)
* [bootstrap](Bootstrap)
* [byteweather](https://byteinsight.co.uk/freebies/weewx-byte-weather-template/)
* [exfoliation](exfoliation)
* [Flat](http://projects.neoground.com/sofaskin) (sofaskin)
* [French Standard skin](https://github.com/weewx/weewx/wiki/weewx-french-standard-skin)
* [Thème graphique standard de weewx en français](https://github.com/weewx/weewx/wiki/Th%C3%A8me-graphique-standard-de-weewx-en-fran%C3%A7ais-%7Bweewx-french-standard-skin-page-in-french%7D) (standard skin in french)
* [WeeRT](https://github.com/tkeffer/weert-js) (experimental live weather feed using Node+React)
* [NeoWX](https://projects.neoground.com/neowx)
* [Niculskin](https://gitlab.com/ngulden/niculskin)
* [Responsive](responsive)
* [simple](simple)
* [Steel Series Gauges](steelseries)
* [Washboard](https://gitlab.com/jquagga/Washboard)
* [weewx-Weather34](https://github.com/steepleian/weewx-Weather34) (real time weather feed, WeeWX port of the popular Weather34 template)
* [weewx-wd](weewx-wd)
* [weeWX Weather App](https://github.com/evilbunny2008/weeWXWeatherApp/wiki) (Android)

#### Generators
* [sftp - Upload data using SFTP (not FTPS)](https://github.com/matthewwall/weewx-sftp)

#### Uploaders
* <img src="http://weewx.com/consumers/emoncms.png" width='16'/> [emoncms](emoncms)
* <img src="http://graphite.readthedocs.org/favicon.ico" width='16'/> [graphite](https://github.com/ampledata/weewx_graphite)
* <img src="https://www.idokep.hu/images/icon/idokep_tab.png" width='16'/> [idokep](https://github.com/lorantkurthy/weewx-idokep/)
* <img src="http://weewx.com/consumers/influx-logo.png" width='16'/> [influxdb](https://github.com/matthewwall/weewx-influx)
* <img src="http://meteotemplate.com/web/logoBlack.png" width='16'/> [meteotemplate](https://github.com/matthewwall/weewx-meteotemplate)
* <img src="http://mqtt.org/favicon.ico" width='16'/> [mqtt](mqtt)
* <img src="http://openweathermap.org/favicon.ico" width='16'/> [openweathermap](openweathermap)
* <img src="http://services.previmeteo.com/fr/assets/img/previmeteo/favicon-previmeteo.jpg" width='16'/> [previmeteo](https://github.com/previmeteo/weewx-previmeteo)
* <img src="https://prometheus.io/assets/favicons/favicon.ico" width='16'/> [prometheus](https://github.com/sulrich/weewx-prompush)
* <img src="https://avatars3.githubusercontent.com/u/739550?v=3&s=200" width='16'/> [pusher](https://github.com/raymondelooff/weewx-pusher)
* <img src="https://rainmachine.com/favicon.ico" width='16'/> [RainMachine](RainMachine)
* <img src="https://www.infoclimat.fr/images/footer_IClogo.png" width='16'/> [Réseau Static de www.infoclimat.fr](https://github.com/AssociationInfoclimat/StatIC-WeeWX)
* <img src="https://rp5.ru/favicon.ico" width='16'/> [RP5](https://github.com/sapegin-o1eg/weewx-rp5)
* <img src="http://smartenergygroups.com/favicon.ico" width='16'/> [smart energy groups](seg)
* <img src="http://thingspeak.com/favicon.ico" width='16'/> [thingspeak](thingspeak)
* <img src="http://twitter.com/favicon.ico" width='16'/> [twitter](twitter)
* <img src="https://www.weather365.net/images/web-104x122.png" width='16'/> [weather365](weather365)
* <img src="http://weatherbug.com/favicon.ico" width='16'/> [weatherbug](weatherbug)
* <img src="http://weewx.com/consumers/weathercloud.ico" width='16'/> [weathercloud](https://github.com/matthewwall/weewx-wcloud)
* <img src="http://wetter.com/favicon.ico" width='16'/> [wetter](wetter)
* <img src="http://windfinder.com/favicon.ico" width='16'/> [windfinder](windfinder)
* <img src="https://stations.windguru.cz/img/apple-touch-icon-precomposed.png" width='16'/> [windguru](https://github.com/claudobahn/weewx-windguru)
* <img src="http://weewx.com/consumers/windy.png" width='16'/> [windy](https://github.com/matthewwall/weewx-windy)
* <img src="http://weewx.com/consumers/xively.ico" width='16'/> [xively/cosm/pachube](xively)

### Development notes
* [Development recipes](https://raw.githubusercontent.com/weewx/weewx/master/DEV_NOTES.txt)
* [Development and testing infrastructure](https://github.com/weewx/weewx/wiki/devtest-infrastructure)
* [Dependency matrix](https://github.com/weewx/weewx/wiki/weewx4-compatibility-matrix)
* [Supporting Python 3](https://github.com/weewx/weewx/wiki/Python-3-strategy)
* [Proposal for logging events in V4](https://github.com/weewx/weewx/wiki/WeeWX-v4-and-logging)
* [Proposal for user-defined types in V4](https://github.com/weewx/weewx/wiki/WeeWX-V4-user-defined-types)
* [Accumulators](https://github.com/weewx/weewx/wiki/Accumulators)