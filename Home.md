## Welcome to the WeeWX wiki!

This is the place to go for information about the many extensions available for WeeWX, as well as random "how to" tips.  For information on how to download, install, and configure WeeWX, see the [WeeWX web site](http://www.weewx.com/).

Edit this page as you see fit; this wiki uses [Markdown](https://help.github.com/articles/github-flavored-markdown/) syntax, not HTML.  To add a new page, reference it within brackets, e.g.: [SamplePage].


### Hardware
[On what hardware do you run WeeWX?](hardware)

### HOWTO Pages
* [Understanding paths](Understanding paths)
* [Convert from setup.py to deb](How%20to%20convert%20from%20setup.py%20install%20to%20debian%20install)
* [Use logwatch to report WeeWX log messages](logwatch)
* [Using WeeWX with a DavisSi1000 radio](DavisSi1000 radio)
* [Colored UV index](Colored%20UV%20index)
* [Cleaning up old 'bad' data](Cleaning up old 'bad' data)
* [Using one-wire sensors with WeeWX](Onewire sensors)
* [Run WeeWX as a non-root user](Run as a non root user)
* [Reduce the number of writes for SD cards or SSDs](Minimize writes on SD cards)
* [Deal with FineOffset USB lockup](FineOffset USB lockup)
* [Install MesoWX live weather feed](https://bitbucket.org/lirpa/mesowx)
* [Transfer from MySQL to sqlite](Transfer%20from%20MySQL%20to%20sqlite/)
* [Transfer from sqlite to MySQL](Transfer%20from%20sqlite%20to%20MySQL)
* [Change from one driver to another](Change driver)
* [Upload missing data to weather underground](http://www.weewx.com/wunderfixer/)
* [Run WeeWX on a Raspberry Pi](Raspberry%20Pi)
* [Manage extensions](extensions)
* [Troubleshoot Pillow](Troubleshooting-Pillow)
* [Add a sensor to any weather station](add-sensor)
* [Difference between `barometer`, `pressure`, and `altimeter`](Barometer,-pressure,-and-altimeter)
* [How to configure weewx to run under systemd](systemd)

### Extensions to weewx

Use extensions to add capabilities to a weewx installation.  Drivers enable weewx to collect data from various sources, services collect and emit data, skins contain the templates for generating reports, and uploaders transfer data to online services.

#### Drivers
* [owfs - Collect data from one-wire sensors](owfs)
* [wanet - Collect data from Wanet Wario ME11/12 meteo station](https://sourceforge.net/projects/wariome11)
* [wlink - Collect data from weatherlink.com](wlink)
* [ipwx - Collect data from AcuLink Internet Bridge via Raspberry Pi](http://nincehelser.com/ipwx/)
* [hackulink - Collect data from AcuLink Internet Bridge via OpenWRT](http://geekfun.com/hackulink/)
* [observerip - Collect data from Ambient WS1001/WS1400/WS1200 ObserverIP (Fine Offset WH2600)](https://github.com/dkmcode/weewx-observerip)
* [w820 - Collect data from Ventus W820 bluetooth station](https://github.com/daduke/ventusw820)

#### Services
* [owfs - Augment weather station data with data from one-wire sensors](owfss)
* [maxbotix - Collect data from maxbotix ultrasonic rangefinder sensors](maxbotix)
* [forecasting - Download, generate, and display forecasts and tides](forecasting)
* [cmon - Monitor CPU, Memory, Disk, and Network on the computer running weewx](cmon)
* [crt - Emit a Cumulus realtime.txt file whenever sensor readings change](crt)
* [cwxn - Emit a Cumulus wxnow.txt file whenever sensor readings change](cwxn)
* [csv - Emit loop or archive data to a file in CSV format](csv)
* [as3935 - Collect lightning strike data from AS3935 sensor attached to RPi](as3935)

#### Skins
* [exfoliation](exfoliation)
* [amphibian](amphibian)
* [simple](simple)
* [weewx-wd](WEEWX-WD)
* [bootstrap](Bootstrap)
* [byteweather](http://davies-barnard.uk/development-and-plugins/weewx-byteweather-template/)
* [Flat](http://en.blauesledersofa.de/2015/03/flat-template-for-weewx/) (sofaskin)
* [Steel Series Gauges](https://github.com/mcrossley/SteelSeries-Weather-Gauges)
* [Responsive](http://carlingfordweather.sydney/)

#### Uploaders
* <img src="http://emoncms.org/Theme/emoncms-logo.png" width='16'/> [emoncms](emoncms)
* <img src="http://graphite.readthedocs.org/favicon.ico" width='16'/> [graphite](https://github.com/ampledata/weewx_graphite)
* <img src="http://mqtt.org/favicon.ico" width='16'/> [mqtt](mqtt)
* <img src="http://openweathermap.org/themes/demo/assets/vendor/owm/images/OWM_logo32_32.png" width='16'/> [openweathermap](openweathermap)
* <img src="http://smartenergygroups.com/favicon.ico" width='16'/> [smart energy groups](seg)
* <img src="http://thingspeak.com/favicon.ico" width='16'/> [thingspeak](thingspeak)
* <img src="http://twitter.com/favicon.ico" width='16'/> [twitter](twitter)
* <img src="http://weatherbug.com/favicon.ico" width='16'/> [weatherbug](weatherbug)
* <img src="http://weathercloud.net/favicon.ico" width='16'/> [weathercloud](weathercloud)
* <img src="http://wetter.com/favicon.ico" width='16'/> [wetter](wetter)
* <img src="http://windfinder.com/favicon.ico"/ width='16'> [windfinder](windfinder)
* <img src="https://personal.xively.com/favicon.ico" width='16'/> [xively/cosm/pachube](xively)

### Obsolete entries

[Install on SheevaPlug](Notes%20on%20porting%20weewx%20to%20the%20SheevaPlug)