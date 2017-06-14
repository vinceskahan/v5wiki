This skin provides a page displaying the [SteelSeries Weather Gauges](https://github.com/mcrossley/SteelSeries-Weather-Gauges).

The preferred method of installation is using the *wee_extension* utility although the skin may also be installed manually.

**Note**: This skin uses a weeWX report to generate the file *gauge-data.txt* which in turn is used to update the *SteelSeries Weather Gauges*. Since weeWX reports are generated only once per archive period, *gauge-data.txt* and the *SteelSeries Weather Gauges* are only updated once per archive period.

### Pre-Requisites

This skin requires weeWX v3.4.0 or later.

### Manual installation

Instructions for manual installation can be found in the [readme.txt](https://github.com/mcrossley/SteelSeries-Weather-Gauges/tree/master/weather_server/WeeWX) file in the weeWX directory of the [SteelSeries Weather Gauges repository](https://github.com/mcrossley/SteelSeries-Weather-Gauges).

### Installation using *wee_extension*

#### Download

1.  Download the latest release extension package from the [releases tab](https://github.com/gjr80/weewx-steelseries/releases) of the [SteelSeries Weather Gauges extension repository](https://github.com/gjr80/weewx-steelseries):

```
$ wget -P /var/tmp https://github.com/gjr80/weewx-steelseries/releases/download/vx.y.z/steelseries-x.y.z.tar.gz
```
#### Installation

1.  Run the extension installer:

        $ wee_extension --install steelseries-x.y.z.tar.gz

2. Configure the *SteelSeries Weather Gauges* for use with weeWX:
    - edit *$SKIN_ROOT/ss/scripts/gauges.js*:
    - locate the following settings and set as indicted below:
    
            weatherProgram : 6,
            imgPathURL : '',
            stationTimeout : 10,        // set to twice archive interval, in minutes
            showUvGauge : true,         // false if there is no UV sensor
            showSolarGauge : true,      // false if there is no solar radiation sensor
            showRoseGauge : false,      // true if field WindRoseData is populated
    
    - save *$SKIN_ROOT/ss/scripts/gauges.js*
    
    **Note**: *$SKIN_ROOT* is the directory where the weeWX skins are saved. This is nominally */home/weewx/skins* or */etc/weewx/skins* depending on your weeWX installation. Refer to [where to find things](http://weewx.com/docs/usersguide.htm#Where_to_find_things) in the weeWX [User's Guide](http://weewx.com/docs/usersguide.htm) for further information.  

3. Restart weeWX:

        $ sudo /etc/init.d/weewx stop
        $ sudo /etc/init.d/weewx start

### Screenshot
![SteelSeries Weather gauges for weeWX](https://github.com/gjr80/weewx-steelseries/blob/master/steelseries%20screenshot.png?raw=true)


