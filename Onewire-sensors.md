<div style="float:right">
<img src="http://weewx.com/hardware/ow-ds9490r.png" width="100" />
<img src="http://weewx.com/hardware/ow-ds18b20.png" width="100" />
<img src="http://weewx.com/hardware/ow-inspeed-anemometer.png" width="100" />
<img src="http://weewx.com/hardware/ow-ads-anemometer.png" width="100" />
<img src="http://weewx.com/hardware/ow-soil-moisture-sensor.png" width="100" />
</div>

There are two basic approaches to collecting data from one-wire devices: driver or service.

In the driver approach, the one-wire system is the primary source of data, whether it is a bunch of one-wire sensors or a one-wire weather station.

In the service approach, data from the one-wire devices augment data collected from a weather station. WeeWX runs in a standard weather station configuration. At every loop or archive interval, the one-wire service collects data from the one-wire devices then augments the loop or archive packet with those data.

Either approach can use the standard WeeWX database schema, or a customized schema.

The owfs.py extension can be used as either a driver or a service.