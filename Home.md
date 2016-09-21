## Welcome to the WeeWX wiki!

This is the place to go for information about the many extensions available for WeeWX, as well as random "how to" tips.  For information on how to download, install, and configure WeeWX, see the [WeeWX web site](http://www.weewx.com/).

Edit this page as you see fit; this wiki uses [Markdown](https://help.github.com/articles/github-flavored-markdown/) syntax, not HTML.  To add a new page, reference it within brackets, e.g.: [SamplePage].


### Hardware
[On what hardware do you run WeeWX?](hardware)

### HOWTO Pages
* [What you should know about paths](Understanding paths)
* [What is the difference between `barometer`, `pressure`, and `altimeter`](Barometer,-pressure,-and-altimeter)

#### weewx configuration
* [Manage extensions](extensions)
* [Change from one driver to another](Change driver)
* [Convert from setup.py to deb](How%20to%20convert%20from%20setup.py%20install%20to%20debian%20install)
* [Colorize the UV index](Colored%20UV%20index)

#### operating system configuration
* [Run WeeWX as a non-root user](Run as a non root user)
* [Put log messages from WeeWX into a separate file](logging)
* [Use logwatch to report WeeWX log messages](logwatch)
* [Configure WeeWX to run under systemd](systemd)
* [Reduce the number of writes for SD cards or SSDs](Minimize writes on SD cards)
* [Troubleshoot Pillow](Troubleshooting-Pillow)

#### data management
* [Clean up old 'bad' data](Cleaning up old 'bad' data)
* [Upload missing data to weather underground](http://www.weewx.com/wunderfixer/)
* [Transfer from MySQL to sqlite](Transfer%20from%20MySQL%20to%20sqlite/)
* [Transfer from sqlite to MySQL](Transfer%20from%20sqlite%20to%20MySQL)

#### hardware
* [Add a sensor to any weather station](add-sensor)
* [Use one-wire sensors with WeeWX](Onewire sensors)
* [Deal with FineOffset USB lockup](FineOffset USB lockup)
* [Take control of your Observer (Fine Offset HP1000/WH2600)](observer)
* [Get your Aercus WeatherSleuth (Observer) working with WeeWX](weathersleuth)
* [Run WeeWX on a Raspberry Pi](Raspberry%20Pi)
* [Run WeeWX on a Raspberry Pi with a read-only file system : RorPi](https://github.com/glennmckechnie/rorpi-raspberrypi/wiki/Rorpi-Home)
* [Raspberry Pi weather station with i2C sensors](Raspberry Pi weather station with i2C sensors)

### Extensions to WeeWX

Use extensions to add capabilities to a weewx installation.  Drivers enable weewx to collect data from various sources, services collect and emit data, skins contain the templates for generating reports, and uploaders transfer data to online services.

#### Drivers
* [airmar - Collect data from Airmar 150WX](https://github.com/fullergalway/weewx-airmar-150wx-driver)
* [hackulink - Collect data from AcuLink Internet Bridge via OpenWRT](http://geekfun.com/hackulink/)
* [interceptor - Collect data from Acurite Bridge, Fine Offset HP1000/WH2600, OS LW301/LW302, or LaCross GW1000U](https://github.com/matthewwall/weewx-interceptor)
* [ipwx - Collect data from AcuLink Internet Bridge via Raspberry Pi](http://nincehelser.com/ipwx/)
* [klimalogg - Collect data from Klimalogg Pro](https://github.com/matthewwall/weewx-klimalogg)
* [meteostick - Collect data from Meteostick](https://github.com/matthewwall/weewx-meteostick)
* [netatmo - Collect data from netatmo](https://github.com/matthewwall/weewx-netatmo)
* [nm150 - Collect data from New Mountain NM150](https://github.com/matthewwall/weewx-nm150)
* [observerip - Collect data from ObserverIP (Fine Offset HP1000)](https://github.com/dkmcode/weewx-observerip)
* [owfs - Collect data from one-wire sensors](owfs)
* [si1000 - Collect data from Si1000 radio receiver (for Davis stations)](DavisSi1000 radio)
* [socketlogger - Collect data from ObserverIP (Fine Offset HP1000) by intercepting traffic](http://obrienlabs.net/redirecting-weather-station-data-from-observerip/)
* [swb - Collect data from SMA Sunny WebBox](https://github.com/matthewwall/weewx-swb)
* [twi - Collect data from Texas Weather Instruments hardware](https://github.com/matthewwall/weewx-twi)
* [w820 - Collect data from Ventus W820 bluetooth station](https://github.com/daduke/ventusw820)
* [wanet - Collect data from Wanet Wario ME11/12 meteo station](https://sourceforge.net/projects/wariome11)
* [wlink - Collect data from weatherlink.com](wlink)

#### Services
* [as3935 - Augment station data with lightning strike data from AS3935 attached to RPi](as3935)
* [cmon - Monitor CPU, Memory, Disk, and Network on the computer running weewx](cmon)
* [crt - Emit a Cumulus realtime.txt file whenever sensor readings change](crt)
* [csv - Emit loop or archive data to a file in CSV format](csv)
* [cwxn - Emit a Cumulus wxnow.txt file whenever sensor readings change](cwxn)
* [forecasting - Download, generate, and display forecasts and tides](forecasting)
* [maxbotix - Augment station data with data from maxbotix ultrasonic rangefinder](maxbotix)
* [owfs - Augment station data with data from one-wire sensors](owfss)
* [opsgenie - Send heartbeats and alerts to cloud alerting service OpsGenie](opsgenie)

#### Skins
* [amphibian](amphibian)
* [bootstrap](Bootstrap)
* [byteweather](http://davies-barnard.uk/development-and-plugins/weewx-byteweather-template/)
* [exfoliation](exfoliation)
* [Flat](http://neoground.com/projects/weewx/) (sofaskin)
* [French Standard skin](https://github.com/weewx/weewx/wiki/weewx-french-standard-skin)
* [MesoWX](https://bitbucket.org/lirpa/mesowx) (live weather feed)
* [Responsive](responsive)
* [simple](simple)
* [Steel Series Gauges](https://github.com/mcrossley/SteelSeries-Weather-Gauges)
* [weewx-wd](WEEWX-WD)

#### Uploaders
* <img src="http://emoncms.org/Theme/emoncms-logo.png" width='16'/> [emoncms](emoncms)
* <img src="http://graphite.readthedocs.org/favicon.ico" width='16'/> [graphite](https://github.com/ampledata/weewx_graphite)
* <img src="http://mqtt.org/favicon.ico" width='16'/> [mqtt](mqtt)
* <img src="http://openweathermap.org/favicon.ico" width='16'/> [openweathermap](openweathermap)
* <img src="https://prometheus.io/assets/favicons/favicon.ico" width='16'/> [prometheus](https://github.com/sulrich/weewx-prompush)
* <img src="https://pusher.com/images/favicon.ico" width='16'/> [pusher](https://github.com/raymondelooff/weewx-pusher)
* <img src="http://smartenergygroups.com/favicon.ico" width='16'/> [smart energy groups](seg)
* <img src="http://thingspeak.com/favicon.ico" width='16'/> [thingspeak](thingspeak)
* <img src="http://twitter.com/favicon.ico" width='16'/> [twitter](twitter)
* <img src="http://weatherbug.com/favicon.ico" width='16'/> [weatherbug](weatherbug)
* <img src="http://weathercloud.net/favicon.ico" width='16'/> [weathercloud](weathercloud)
* <img src="http://wetter.com/favicon.ico" width='16'/> [wetter](wetter)
* <img src="http://windfinder.com/favicon.ico" width='16'/> [windfinder](windfinder)
* <img src="https://stations.windguru.cz/img/apple-touch-icon-precomposed.png" width='16'/> [windguru](https://github.com/claudobahn/weewx-windguru)
* <img src="https://personal.xively.com/favicon.ico" width='16'/> [xively/cosm/pachube](xively)

### Obsolete entries

[Install on SheevaPlug](Notes%20on%20porting%20weewx%20to%20the%20SheevaPlug)