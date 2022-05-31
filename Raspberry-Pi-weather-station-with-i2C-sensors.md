# Overview #
I am in the process of updating my configuration and will update this file when completed.

The following will describe a weather station utilising i2C sensors for (temperature, humidity, pressure, UV and solar radiation, rain data by a one-wire Rainwise bucket and the wind direction and strength from a Davis mast head unit. I also now collect my aircon data and import this into weewx. Note this is an updated link (using Belchertown skin). 
I have also added the following tabs to the Belchertown skin; SEQ detailed forcast uses code developed by Darryn Capes-Davis for his responsive skin for weewx and a colour history chart by Nick Dajda used in his Bootstrap skin for weewx. Edited 23 Feb 2021
[Link to my station](https://bit.ly/2A8BbOx)

## Overall block diagram #
[Link to block diagram](https://bit.ly/3t6id3o)

## Equipment used and general information #
#### Raspberry Pi3 #
#### FARS (my variation of a design found on the web) #
#### i2C sensors and i2C range extenders #
###### Range Extenders P82B715 #
I have two runs (as shown on the block diagram) and with these extenders I have no problems. I saw one report of 50 metre cabling in use. I have had no problems with my configuration. The termination requirements etc will be detailed on the schematic.
###### Adafruit Sensiron SHT31-D Temperature & Humidity sensor breakout board #
###### Adafruit ADS1115 16-bit ADC - 4 channel breakout board #
###### Adafruit BMP085 i2C Barometric Pressure and temperature breakout board #
###### Adafruit SI1145 Digital UV index #
###### i2C Bi-Directional level converter #
#### Other sensors #
###### Davis Anemometer #
###### Rainwise one-wire rain bucket #

### General Information #
My current installation is a raspberry Pi3 booting from an external HDD. I have a ramdisk with services for gathering the sensor data from the i2C sensors, aircon status, and the fan speed from my Fan Assisted Radiation sensor (FARS).
With my Pi3 defaulted to python3 another method of getting my rain gauge (one wire sensor) information into weewx had to be used and will be detailed later. (Python3 no longer has a OW module available)

### Pi Configuration #
This is the basic setup for my Pi with HDD and RAM disk and enabled i2c.
[Pi configuration](https://bit.ly/3GxhTQu)

### Australian weather forecast added to Belchertown skin
This is a link to the files and other information to enable a localised weather forecast and a colour history chart to be displayed within the Belchertown skin for weewx.
[Belchertown edits](https://bit.ly/3t4pKzA)

### Weewx config #
**still to be updated**
Use your normal config procedure, this link is the procedure I use. [weewx install](http://bit.ly/2F5CUjT)

### Python scripts required for installation of i2C sensors and required services #

_**My new installation did not need all these scripts, also under python3 several test scripts need to have their print statements corrected, also some error trapping may have to be corrected....apart from that all my active scripts performed without a problem under python3...all scripts left here , they may be useful.**_
*[Python and service scripts for i2C sensors, FARS control, Aircon data and Weewx driver](http://bit.ly/2F5mpEA)

*[Fan control and sensor data collection services](http://bit.ly/2HPSpPX)

### Schematics and construction information #
*[i2C BUS](http://bit.ly/2F4zwGf)

*[wind speed and direction](https://bit.ly/3kQO57L)

*[FARS control & fan rotation FB & RTC](http://bit.ly/2HR3jFt)

*[FARS construction](http://bit.ly/2HkfmOj)

*[Davis wind, wmr200a and rainwise sensors](http://bit.ly/2vy4Q0l)