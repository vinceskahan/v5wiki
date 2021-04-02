The 1-Wire bus is a cheap low-speed bus for devices like weather sensors, access control, etc. It can be attached to your system via serial, USB, I2C, and other interfaces.

<div style="float:right">
<img src="http://weewx.com/hardware/ow-ds9490r.png" width="100" />
<img src="http://weewx.com/hardware/ow-ds18b20.png" width="100" />
<img src="http://weewx.com/hardware/ow-inspeed-anemometer.png" width="100" />
<img src="http://weewx.com/hardware/ow-ads-anemometer.png" width="100" />
<img src="http://weewx.com/hardware/ow-soil-moisture-sensor.png" width="100" />
</div>

## Supported devices:
As of 2017 these sensors are becoming a little harder to find, but they are still available through various means. Ebay and [Sheepwalk Electronics](http://www.sheepwalkelectronics.co.uk) come to mind. Sadly [Hobby boards](http://www.hobbyboards.com/) has gone. 
There are other sources, hit your favorite search engine to find them.

Besides the equipment shown in the above images, there are.
* DS2409 chips: used in various hubs, examples of which are: Hobby-boards (6-port hub), AAG (TAI-8595).
* DS18S20 and variants (2nd image): These are readily available via ebay or other sources and are very effective as temperature sensors.
* DS2438 counter: used in the AAG and Hobby-board designs. The anemometer, wind vane, rain gauge, lightning counter, humidity, solar radiation and barometer from HB use these in some form, as did the AAG equivalents. 
* Hobby boards UVI sensor
* Anemometer and wind vanes: The Inspeed and ADS(?) units have been modified to work with the DS2438 Counters by using the Hobby-boards interface. AAG also had several revisions of their particular unit.   
* [1-wire-pi-plus](https://www.abelectronics.co.uk/p/60/1-wire-pi-plus) is an i2c-1 adapter for the pi.

The owfs module for weewx uses the python bindings (python-ow) for the [OWFS 1-Wire support library](http://owfs.org/)

The owfs software is not necessary for the original python-ow module; for those earlier versions the owftpd, owhttpd, and owserver services are not used and do not need to be running.  In fact, it is safer to leave those services disabled to ensure that they do not conflict with weewx when it attempts to read one-wire devices using the python-ow module.

On the other hand, the conversion to python3 and the disappearance of the equivalent python3-ow bindings, and their replacement with the pyownet module has made the owserver an essential requirement. The owserver component of OWFS needs to be configured and running so that the weewx owfs driver can use it to access the hardware interface.

pyownet and owserver can be also be used with python2.x versions


## Driver or Service
There are two basic approaches to collecting data from one-wire devices: driver or service.

In the driver approach, the one-wire system is the primary source of data, whether it is a bunch of one-wire sensors or a one-wire weather station.

In the service approach, data from the one-wire devices augment data collected from a weather station. WeeWX runs in a standard weather station configuration. At every loop or archive interval, the one-wire service collects data from the one-wire devices then augments the loop or archive packet with those data.

Either approach can use the standard WeeWX database schema, or a customized schema.

To achieve this, the one extension file, owfs.py, is used as either the driver ([[owfs]]) or service ([[owfss]]).