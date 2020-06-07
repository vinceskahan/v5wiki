# Overview #
I am in the process of updating my configuration and will update this file when completed.

The following will describe a weather station utilising i2C sensors for (temperature, humidity, pressure, UV and solar radiation, rain data by a one-wire Rainwise bucket and the wind direction and strength from a Davis mast head unit. I have also now started to collect data from our home aircon, data gathered service at boot and imported by a weewx service.Note this is an updated link 7 June 2020
[Link to my station](https://bit.ly/2A8BbOx)

## Overall block diagram #
[Link to block diagram](http://bit.ly/2HiSESR)


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
###### Sandbox i2C DS1307 RTC module #
###### i2C Bi-Directional level converter #
#### Other sensors #
###### Davis Anemometer #
###### Rainwise one-wire rain bucket #

### General Information #
My configuration includes RTC, external HDD (Boot from SD card fully run on the HDD), Temp RAM disk for logging of data, weewx 3.7.1 and my skin based on the work of Darryn Capes-Davis (responsive skin) also using MySQL DB with replication running to backup weewx database to a QNAP NAS in almost real time. This replication procedure allows for loss and then restarting of NAS...the DB backup will automatically get data from its last record to the present time.

### Pi Configuration #
The following link will cover multiple configs, not all are required and have been in some form covered elsewhere. This is the config I now use. I include it here as it brings together most things I have needed on several installs on the Pi.
[Pi configuration](http://bit.ly/2HlED6R)

### Weewx config #
Use your normal config procedure, this link is the procedure I use. [weewx install](http://bit.ly/2F5CUjT)

### Python scripts required for installation of i2C sensors and required services #
*[prerequisites and ADS1115 python script](http://bit.ly/2F4D8bj)

*[Python and service scripts for i2C sensors FARS control Aircon data Weewx config and Weewx driver](http://bit.ly/2F5mpEA)

*[Fan control and sensor data collection services](http://bit.ly/2HPSpPX)

### Schematics and construction information #
*[i2C BUS](http://bit.ly/2F4zwGf)

*[wind speed and direction](http://bit.ly/2HQngMB)

*[FARS control & fan rotation FB & RTC](http://bit.ly/2HR3jFt)

*[FARS construction](http://bit.ly/2HkfmOj)

*[Davis wind, wmr200a and rainwise sensors](http://bit.ly/2vy4Q0l)