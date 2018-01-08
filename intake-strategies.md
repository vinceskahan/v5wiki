# Strategies for getting data into weeWX

As you may have noticed, weeWX is a pretty flexible bit of code.  Nominally it is a data collector - it pulls data from hardware and puts the data into a database.  But it can also process the data before storing it, for example by apply 'Quality Control' rules or by calculating derived observations such as windchill or heat index.

WeeWX also includes a reporting engine.  Once the data are in the database, weewx can generate HTML pages and images to display current conditions or historical values.

And weeWX can emit data for use elsewhere, either automatically as part of its reporting or uploading system, or manually using the wee_* utilities or raw SQL queries.

The following sections enumerate some options for the first part - how to get data into weeWX.

## Standalone: Driver for directly-connected data source

<table>
<tr>
<td  width="200">
<a href="http://weewx.com/cfg/weewx-data-direct.png">
<img src="http://weewx.com/cfg/weewx-data-direct.png"/>
</a>
</td>
<td>
<p>
In its default configuration, weeWX uses a single driver to collect data from a single weather station.  The weather station typically has a suite of sensors, such as anemometer for wind speed and direction, thermometer for temperature, etc.  Some weather stations have a fixed set of sensors, other stations can use a variety of sensor configurations.  There is a weeWX driver for each type of station, and the driver "knows" the capabilities and configurations of the hardware for which it is designed.
</p>
<p>
Most weather stations are connected directly to the computer on which weeWX is running.  This might be with a USB cable, a serial cable for RS232, a serial cable for ModBus or SDI12, or even an ethernet cable for TCP/IP.  The driver uses whatever protocol is defined by the hardware designer to get data from the sensors.
</p>
<p>
Examples: Ultimeter, TWI, Visala
</p>
</td>
</tr>
</table>


## Standalone: Driver for directly-connected logger

<table>
<tr>
<td  width="200">
<a href="http://weewx.com/cfg/weewx-data-logger.png">
<img src="http://weewx.com/cfg/weewx-data-logger.png"/>
</a>
</td>
<td>
<p>
Some hardware includes a data logger.  If weeWX shuts down, the logger will continue to collect data.  When weeWX starts up, it will read data from the logger since the last time it collected data, so there will be no gaps in the data!
</p>
</td>
</tr>
</table>


## Standalone: Driver for directly-connected extensible data source

<table>
<tr>
<td  width="200">
<a href="http://weewx.com/cfg/weewx-data-logger-extensible.png">
<img src="http://weewx.com/cfg/weewx-data-logger-extensible.png"/>
</a>
</td>
<td>
<p>
Some hardware is directly extensible - you can add sensors and the hardware will recognize them.  In some cases this might be additional temperature/humidity sensors.  For other stations, you can add soil moisture, leaf wetness, solar radiation, or other types of sensors.
</p>
</td>
</tr>
</table>


## Standalone: Driver with services for additional data sources

<table>
<tr>
<td  width="200">
<a href="http://weewx.com/cfg/weewx-data-services.png">
<img src="http://weewx.com/cfg/weewx-data-services.png"/>
</a>
</td>
<td>
<p>
Sometimes a single weather station does not provide enough types of sensors.  For example, how would you add a snow depth sensor to an Acurite 5in1 with console?  Use a weeWX service!  A weeWX data collection service can add observations to each LOOP or archive record.  So you can add data from a snow depth sensor and a tide level sensor to data from an Acurite 5in1 station, then the reports will show integrated data.
</p>
</td>
</tr>
</table>


## Standalone: Driver for network-connected data source

<table>
<tr>
<td  width="200">
<a href="http://weewx.com/cfg/weewx-data-cloud.png">
<img src="http://weewx.com/cfg/weewx-data-cloud.png"/>
</a>
</td>
<td>
<p>
Davis introduced the WeatherLinkIP service in the mid-2000s - when connected to a TCP/IP interface, a Vantage station can send weather data directly to Davis' WeatherLink service.  Other manufacturers such as Rainwise, WeatherHawk, and Columbia offered similar adapters to capture the (typically RS232) output from their sensors and send it directly to their respective data capture services.  Low-end manufacturers such as Fine Offset,  Acurite, and NetAtmo have taken this approach even further - their direct-to-internet models will *only* communicate over TCP/IP.

There are drivers for weeWX that will get data from many of these direct-to-internet stations.

Examples: netatmo, myacurite, weatherlink
</p>
</td>
</tr>
</table>


## Standalone: Driver for network-connected data source

<table>
<tr>
<td  width="200">
<a href="http://weewx.com/cfg/weewx-data-intercept.png">
<img src="http://weewx.com/cfg/weewx-data-intercept.png"/>
</a>
</td>
<td>
<p>
In some cases it is possible to redirect and/or intercept the communication from direct-to-internet stations.  As long as the hardware manufacturer has not resorted to encryption or other user-hostile practices, weeWX can capture the data from these stations so that you can own and manage your own data, no matter what might happen to the internet service or the company that provides it.

Examples: FineOffset Observer, LaCrosse GW1000U, Acurite Bridge/SmartHUB
</p>
</td>
</tr>
</table>



## Multiple instances

<table>
<tr>
<td  width="200">
<a href="http://weewx.com/cfg/weewx-data-multi.png">
<img src="http://weewx.com/cfg/weewx-data-multi.png"/>
</a>
</td>
<td>
<p>
Another way to collect data from multiple data sources is to run a weeWX instance for each source, then use the weeWX reporting architecture to generate a report with data from all of the sources.
</p>
</td>
</tr>
</table>


## Mixing it with other hardware

<table>
<tr>
<td  width="200">
<a href="http://weewx.com/cfg/weewx-data-.png">
<img src="http://weewx.com/cfg/weewx-data-.png"/>
</a>
</td>
<td>
<p>
</p>
</td>
</tr>
</table>

weather station plus arduinos, composite report.  feed a sqlite database from somewhere other than weewx, then use that in a weewx report.

