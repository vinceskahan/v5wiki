### Maxbotix Service

<img src="http://weewx.com/hardware/maxbotix.png" width="200" align="right" />

Maxbotix ultrasonic sensors can detect ranges from 3 to 10 meters with resolution of 1 cm to 1 mm.  The sensors are useful for measuring snow depth, tide height, proximity, and many other range-finding applications.

http://maxbotix.com/

This is a service for weewx that collects data from maxbotix sensors with the RS232 interface.

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-maxbotix-0.5.tgz

### How to Install

1.  Run the extension installer:

```
wee_extension --install weewx-maxbotix-x.y.tgz
```

2.  Modify weewx.conf:

    ```
    [Maxbotix]
        port = /dev/ttyUSB0
        model = MB1040
```

3. Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Caveats

The maxbotix extension can be used as a service or as a driver.

There are many different sensor models, each with different range and units.  Use the `model` parameter to specify which type of maxbotix sensor you are using.

The maxbotix sensors cannot detect items close to the sensor.  For some sensors, the closest limit is 20 cm, for others it is 30 cm.  If an item ranges closer than this distance, the sensor will read 200 or 300, respectively.

### References

Article about maxbotix sensor used to measure snow depth:

http://www.wxforum.net/index.php?topic=24547.msg235695#msg235695

Using a maxbotix sensor to measure tide level:

http://makerzone.mathworks.com/producthardware-detail/real-timetide-gauge-to-tweet-tidal-alerts/

Maxbotix instructions for connecting a sensor to a raspberry pi:

http://maxbotix.com/articles/074.htm