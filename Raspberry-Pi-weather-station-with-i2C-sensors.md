# Overview #

I am again almost fully operational after a lot of work
The following will describe a weather station utilising i2C sensors for (temperature, humidity, pressure, UV and solar radiation, rain data by a one-wire Rainwise bucket and the wind direction and strength from a Davis mast head unit. I also now collect my aircon data and import this into weewx. 
I have also added the following tabs to the Belchertown skin; SEQ detailed forcast uses code developed by Darryn Capes-Davis for his responsive skin for weewx and a colour history chart by Nick Dajda used in his Bootstrap skin for weewx. Edited 26 Aug 2023
[Link to station](https://bit.ly/44v9302)

## Overall block diagram #
[Link to block diagram](https://bit.ly/3t6id3o)

## Equipment used and general information #
#### Raspberry Pi4 #
#### FARS (my variation of a design found on the web) #
#### i2C sensors and i2C range extenders #
###### Range Extenders P82B715 #
I have two runs (as shown on the block diagram) and with these extenders I have no problems. I saw one report of 50 metre cabling in use. I have had no problems with my configuration. The termination requirements etc will be detailed on the schematic.
###### Adafruit Sensiron SHT31-D Temperature & Humidity sensor breakout board #
###### DFRobot weather proof DHT31x Temperature & Humidity sensor #
###### Adafruit ADS1115 16-bit ADC - 4 channel breakout board #
###### Adafruit BMP085 i2C Barometric Pressure and temperature breakout board #
###### i2C Bi-Directional level converter #
#### Other sensors #
###### Davis Anemometer #
###### Rainwise one-wire rain bucket #

### General Information #
My current installation is a raspberry Pi4 booting from an external SSD. I have a ramdisk with services for gathering the sensor data from the i2C sensors, aircon status, and the fan speed from my Fan Assisted Radiation sensor (FARS).
I had a failure of my external SHT31-D sensor and have installed a "water proof" version of the SHR31 by DFRobot. I had some difficulty finding a python script suitable for my Pi... here is a link to a folder holding all the information supplied by DFRobot
[DFROBOT_SHT3x-master](https://bit.ly/45clGyw).
My Pi4 is now running software gathering data from my Solar system and energy consumed in near real time and house water consumption.
Weewx and a MQTT broker is also installed. MQTT information from weewx and my power monitoring system is combined and sent to another Pi3 running home assistant. I have not secured the MQTT so station updates by refreshing from external requests.
The following link will contain new and updated files as I create them. The initial info is the configuration I now use as well as solar 
and power install info. Weather info will follow.
[link to folder with updated info](https://bit.ly/3KE7t53)

### Pi Configuration #
This has changed considerably I will update later.
This is the basic setup for my Pi with HDD and RAM disk and enabled i2c.
[Pi configuration](https://bit.ly/3GxhTQu)

### Australian weather forecast added to Belchertown skin
This is a link to the files and other information to enable a localised weather forecast and a colour history chart to be displayed within the Belchertown skin for weewx. This has changed considerably so removed link will update later.

### Weewx config #
**still to be updated**
Use your normal config procedure.

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