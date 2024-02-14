This skin provides a page displaying the [SteelSeries Weather Gauges](https://github.com/mcrossley/SteelSeries-Weather-Gauges).

The preferred method of installation is using the *weectl* utility although the skin may also be installed manually.

**Note**: This skin uses a WeeWX report to generate the file *gauge-data.txt* which in turn is used to update the *SteelSeries Weather Gauges*. Since WeeWX reports are generated only once per archive period, *gauge-data.txt* and the *SteelSeries Weather Gauges* are only updated once per archive period.

### Pre-Requisites

This skin requires WeeWX v3.4.0 or later.

### Installation using *weectl*

#### Download

1.  Download the latest release extension package from the [releases tab](https://github.com/gjr80/weewx-steelseries/releases) of the [SteelSeries Weather Gauges extension repository](https://github.com/gjr80/weewx-steelseries):

```
$ wget -P /var/tmp https://github.com/gjr80/weewx-steelseries/releases/download/vx.y.z/steelseries-x.y.z.tar.gz
```
#### Installation

1.  Run the extension installer:

        $ weectl extension install steelseries-x.y.z.tar.gz

2. Restart WeeWX:

        $ sudo systemctl stop weewx
        $ sudo systemctl start weewx

### Manual installation

Instructions for manual installation can be found in the [readme.txt](https://github.com/mcrossley/SteelSeries-Weather-Gauges/tree/master/weather_server/WeeWX) file in the WeeWX directory of the [SteelSeries Weather Gauges repository](https://github.com/mcrossley/SteelSeries-Weather-Gauges).

### Screenshot
![SteelSeries Weather gauges for WeeWX](https://github.com/gjr80/weewx-steelseries/blob/master/steelseries%20screenshot.png?raw=true)


