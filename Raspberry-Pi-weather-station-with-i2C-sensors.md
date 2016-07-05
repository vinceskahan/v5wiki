#Overview#
The following will describe a weather station utilising i2C sensors for (temperature, humidity, pressure, UV and solar radiation), rain data by a one-wire Rainwise bucket and the wind direction and strength from a Davis mast head unit.

##Overall block diagram#
[Link to block diagram](https://goo.gl/6ffgOl)


##Equipment used and general information#
####Raspberry Pi3#
####FARS (my variation of a design found on the web)#
####i2C sensors and i2C range extenders#
######Range Extenders P82B715#
I have two runs (as shown on the block diagram) and with these extenders I have no problems. I saw one report of 50 metre cabling in use. I have had no problems with my configuration. The termination requirements etc will be detailed on the schematic.
######Adafruit Sensiron SHT31-D Temperature & Humidity sensor breakout board#
######Adafruit ADS1115 16-bit ADC - 4 channel breakout board#
######Adafruit BMP280 i2C Barometric Pressure and temperature breakout board#
######Adafruit TSL2591 High Dynamic range light sensor#
######Adafruit SI1145 Digital UV index#
######Sandbox i2C DS1307 RTC module#
######i2C Bi-Directional level converter#
####Other sensors#
######Davis Anemometer#
######Rainwise one-wire rain bucket#

###General Information#
My configuration includes RTC, external HDD (Boot from SD card fully run on the HDD), Temp RAM disk for logging of data, weewx 3.5, forecast module, MySQL DB with replication running to backup DB to a QNAP NAS in almost real time. This replication procedure allows for loss and then restarting of NAS...the DB backup will automatically get data from its last record to the present time.

###Pi Configuration#
The following link will cover multiple configs, not all are required and have been in some form covered elsewhere. This is the config I now use. I include it here as it brings together most things I have needed on several installs on the Pi.
[Pi configuration](https://goo.gl/9iFNgQ)

###Weewx config#
Use your normal config procedure, this link is the procedure I use. [weewx install](https://goo.gl/YdZp29)

###Python scripts required for installation of i2C sensors#
######[needed scripts](https://goo.gl/WCN0Iw)#




