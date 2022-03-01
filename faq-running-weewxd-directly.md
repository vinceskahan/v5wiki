Frequently users are asked to run weewxd directly from the command line and post a few LOOP messages in their problem reports.

### How to do this

```
Run one of the two following commands

   # for pre-packaged weewx installations
   sudo python3 /usr/share/weewx/weewxd /etc/weewx/weewx.conf

   # or for setup.py weewx installations
   sudo python3 /home/weewx/bin/weewxd /home/weewx/weewx.conf

Let a couple LOOP messages be displayed, then hit control-C to stop weewxd.
Capture the output into your problem report.
```

### Sample output from a Simulator station running pre-packaged weewx

```
sudo python3 /usr/share/weewx/weewxd /etc/weewx/weewx.conf
LOOP:   2022-03-01 18:01:11 UTC (1646157671) UV: 0, altimeter: 31.512244607605663, appTemp: 28.46010918752483, barometer: 31.099999998967093, cloudbase: 1609.323176426526, consBatteryVoltage: 12.801672167457717, dateTime: 1646157671, dewpoint: 27.203560993945757, heatindex: 32.67858297022247, heatingVoltage: 12.397794916672359, humidex: 32.67858297022247, inDewpoint: 31.079533828013187, inHumidity: 29.999999958683617, inTemp: 63.00000002065819, inTempBatteryStatus: 0, maxSolarRad: None, outHumidity: 79.99999996901272, outTemp: 32.67858297022247, outTempBatteryStatus: 0, pressure: 31.099999998967093, radiation: 0, rain: 0, rainBatteryStatus: 0, rainRate: 0.0, referenceVoltage: 12.80378328495448, rxCheckPercent: 76.51021004194791, supplyVoltage: 10.925022055677033, txBatteryStatus: 0, usUnits: 1, windBatteryStatus: 0, windDir: 359.9999998140763, windGust: 6.197456769996279e-09, windGustDir: 359.9999998140763, windSpeed: 5.164547900449179e-09, windchill: 32.67858297022247
LOOP:   2022-03-01 18:01:14 UTC (1646157674) UV: 0, altimeter: 31.512244599253428, appTemp: 28.45807365032865, barometer: 31.099999990703814, cloudbase: 1609.3127063099937, consBatteryVoltage: 12.130725407245396, dateTime: 1646157674, dewpoint: 27.20178958368346, heatindex: 32.67676549144743, heatingVoltage: 11.855704130774878, humidex: 32.67676549144743, inDewpoint: 31.07953369929752, inHumidity: 29.999999628152565, inTemp: 63.00000018592372, inTempBatteryStatus: 0, maxSolarRad: None, outHumidity: 79.99999972111442, outTemp: 32.67676549144743, outTempBatteryStatus: 0, pressure: 31.099999990703814, radiation: 0, rain: 0, rainBatteryStatus: 0, rainRate: 0.0, referenceVoltage: 11.336238007114055, rxCheckPercent: 76.51021004194791, supplyVoltage: 11.275405485314263, txBatteryStatus: 0, usUnits: 1, windBatteryStatus: 0, windDir: 359.9999983266865, windGust: 5.577711537085861e-08, windGustDir: 359.9999983266865, windSpeed: 4.648092932768577e-08, windchill: 32.67676549144743


^CTraceback (most recent call last):
  File "/usr/share/weewx/weewxd", line 264, in <module>
    main()
  File "/usr/share/weewx/weewxd", line 157, in main
    engine.run()
  File "/usr/share/weewx/weewx/engine.py", line 208, in run
    for packet in self.console.genLoopPackets():
  File "/usr/share/weewx/weewx/drivers/simulator.py", line 162, in genLoopPackets
    time.sleep(sleep_time)
KeyboardInterrupt
```