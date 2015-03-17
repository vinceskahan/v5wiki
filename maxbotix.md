### Maxbotix Service

Maxbotix ultrasonic sensors can detect ranges from 3 to 10 meters with resolution of 1 cm to 1 mm.  The sensors are useful for measuring snow depth, tide height, proximity, and many other range-finding applications.

http://maxbotix.com/

This is a service for weewx that collects data from maxbotix sensors with the RS232 interface.

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-maxbotix-0.1.tgz

### How to Install

2.  Run the extension installer:

    ```
setup.py install --extension weewx-maxbotix-x.y.tgz
```

5.  Modify weewx.conf:

    Specify the interface, sensor map and possibly sensor types:

    ```
    [Maxbotix]
        port = /dev/ttyUSB0
```

6. Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Caveats

The maxbotix sensors cannot detect items close to the sensor.  For some sensors, the closest limit is 20 cm, for others it is 30 cm.  If an item ranges closer than this distance, the sensor will read 200 or 300, respectively.
