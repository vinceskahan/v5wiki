Bootstrap template with simple gauges (for Weewx v4)
==================

This skin uses the Bootstrap front end and apache eCharts create sophisticated gauge and charts representations of the current weather, and a colourful monthly history summary. It can be configured to work with MQTT and display live weather data with moving gauges and interactive charts.

To see a live example of this skin, visit this weather site for "Rif bei Hallein, AT":

https://www.kainzbauer.net/weather/Rif/

Before we begin...
--------
1. This has been tested on weewx version 4.10.1 running on Raspberry Pi OS.
2. Works with sqlite, mysql, and any other weewx supported database.

Install
--------
Use the WeeWX extension installer:

~~~~~~
weectl extension install https://github.com/brewster76/fuzzy-archer/archive/refs/heads/master.zip
~~~~~~

Web content is stored in `public_html/Bootstrap`.

More information
--------

Can be found on the github page here: https://github.com/brewster76/fuzzy-archer/blob/master/INSTALL


Screenshots
-----

1. *Front page*
![Front page](https://kainzbauer.net/example_rif.png)

2. *History page*
![History page](https://kainzbauer.net/example_history_rif.png)