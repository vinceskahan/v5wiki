There are two basic approaches to collecting data from one-wire devices: driver or service.

The driver approach uses a weewx driver such as owfs.py. In this approach, the one-wire system is the primary source of data, whether it is a bunch of one-wire sensors or a one-wire weather station.

The service approach uses a weewx service such as owfss.py {[look at owfss driver page](owfss driver) } In this approach, data from the one-wire devices augment data collected from a weather station. WeeWX runs in a standard weather station configuration. At every loop or archive interval, the one-wire service collects data from the one-wire devices then augments the loop or archive packet with those data.

Either approach can use the standard WeeWX database schema, or a customized schema.