# Strategies for getting data into weeWX

As you may have noticed, weeWX is a pretty flexible bit of code.  Nominally it is a data collector - it pulls data from hardware and puts the data into a database.  But it can also process the data before storing it, for example by apply 'Quality Control' rules or by calculating derived observations such as windchill or heat index.

WeeWX also includes a reporting engine.  Once the data are in the database, weewx can generate HTML pages and images to display current conditions or historical values.

And weeWX can emit data for use elsewhere, either automatically as part of its reporting or uploading system, or manually using the wee_* utilities or raw SQL queries.

The following sections enumerate some options for the first part - how to get data into weeWX.

## Standalone: Driver for hardware data source

In its default configuration, weeWX uses a single driver to collect data from a single weather station.  The weather station typically has a suite of sensors, such as anemometer for wind speed and direction, thermometer for temperature, etc.  Some weather stations have a fixed set of sensors, other stations can use a variety of sensor configurations.  There is a weeWX driver for each type of station, and the driver "knows" the capabilities and configurations of the hardware for which it is designed.

Most weather stations are connected directly to the computer on which weeWX is running.  This might be with a USB cable, a serial cable for RS232, a serial cable for ModBus or SDI12, or even an ethernet cable for TCP/IP.  The driver uses whatever protocol is defined by the hardware designer to get data from the sensors.

Examples: Vantage, FineOffset 10x0/20x0/30x0, Acurite, Ultimeter, Rainwise, TWI, LaCrosse, Hideki

## Standalone: Driver for network-based data source

Davis introduced the WeatherLinkIP service in the mid-2000s - when connected to a TCP/IP interface, a Vantage station can send weather data directly to Davis' WeatherLink service.  Other manufacturers such as Rainwise, WeatherHawk, and Columbia offered similar adapters to capture the (typically RS232) output from their sensors and send it directly to their respective data capture services.  Low-end manufacturers such as Fine Offset,  Acurite, and NetAtmo have taken this approach even further - their direct-to-internet models will *only* communicate over TCP/IP.

There are drivers for weeWX that will get data from many of these direct-to-internet stations.  In some cases, weeWX can intercept the network traffic.  In other cases, the vendors encrypt or otherwise obfuscate the data, so weeWX must use APIs to download data from the vendor instead of getting it directly from the hardware.

Examples: FineOffset Observer, LaCrosse GW1000U, Acurite Bridge/SmartHUB

Examples: netatmo, myacurite, weatherlink

## Standalone: Driver with services

Sometimes a single weather station does not provide enough types of sensors.  For example, how would you add a snow depth sensor to an Acurite 5in1 with console?  Use a weeWX service!  A weeWX data collection service can add observations to each LOOP or archive record.  So you can add data from a snow depth sensor and a tide level sensor to data from an Acurite 5in1 station, then the reports will show integrated data.

## Multiple instances

weather station plus multiple ultrasonic sensors, composite report

## Mixing it with other hardware

weather station plus arduinos, composite report.  feed a sqlite database from somewhere other than weewx, then use that in a weewx report.

