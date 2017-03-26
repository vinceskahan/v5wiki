### OWFS Driver

OWFS (one-wire file system) provides access to data from one-wire sensors as if they were files in the computer's file system.

This is a driver for weewx that collects data from one-wire sensors using owfs.

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-owfs-0.20.tgz

### How to Install

1.  Install the python bindings.  For example, on debian systems:

```
sudo apt-get install python-ow
```

2.  Run the extension installer:

```
wee_extension --install weewx-owfs-x.y.tgz
```

3.  Find out what sensors are attached:

```
# if you installed using setup.py:
sudo PYTHONPATH=/home/weewx/bin python /home/weewx/bin/user/owfs.py --sensors

# if you installed from rpm or deb package:
sudo PYTHONPATH=/usr/share/weewx python /usr/share/weewx/user/owfs.py --sensors
```

4.  Get some data from the sensors:

```
# if you installed using setup.py:
sudo PYTHONPATH=/home/weewx/bin python /home/weewx/bin/user/owfs.py --readings

# if you installed from rpm or deb package:
sudo PYTHONPATH=/usr/share/weewx python /usr/share/weewx/user/owfs.py --readings
```

5.  Modify weewx.conf:

Specify the driver, interface, sensor map and possibly sensor types:

```
    [OWFS]
        interface = u
        driver = user.owfs
        [[sensor_map]]
            outTemp = /uncached/28.8A071E050000/temperature
            UV = /uncached/EE.1F20CB020800/UVI/UVI
            radiation = /26.FB67E1000000/S3-R1-A/luminosity
            lightning = /1D.1AD00F000000/counters.A
        [[sensor_type]]
            lightning = counter
```

Specify OWFS as the driver:

```
    [Station]
        ...
        station_type = OWFS
```

Add calibration corrections for individual sensors:

```
    [StdCalibrate]
        [[Corrections]]
            radiation = radiation * 1.7304636.  `
```

6. Start weewx

```
sudo /etc/init.d/weewx start
```

### Caveats

The interface defaults to 'u', which is short for 'usb'.  This is the interface for a DS9490R USB adaptor.  Other options for interface include a serial port such as '/dev/ttyS0', or a remote one-wire server such as 'remote_system:3003'.

If you are using a one-wire-to-USB adapter, then the default interface of 'u' should work.  If your one-wire devices are attached via serial port, i2c, or some other physical interface, then you will have to specify the interface when running owfs.py direction and when configuring owfs.py to run as a service or driver.  Use the ```--iface``` option on the command-line or the ```interface``` option in the OWFS section of weewx.conf.

See the comments in owfs.py for additional options and for examples of how to configure for specific devices.

The sensor map is simply an association between fields in the weewx database schema and paths to one-wire devices.  Preface a path with /uncached to skip the cache.

The default sensor type is 'gauge' - data from the sensor are saved directly.  Other types include 'delta' (difference between current and last samples), 'counter' (similar to delta, but always increasing), and 'average' (delta divided by sample period). Some specific one-wire devices are also supported such as Hobby Boards ADS and Inspeed anemometers and AAG anemometer.  See the comments in owfs.py for details and sample configurations.

By default, this driver assumes that all data from the sensors are in metric units.  Use the _unit_system_ option if the sensors return data in US customary units.

The driver will work only with sensor values that can be converted to float.

Use the corrections in StdCalibrate to calibrate raw data.

The ow utilities owftpd, owhttpd, and owserver are part of the owfs package, not part of the weewx owfs extension.  They might be helpful for debugging and diagnostics, but they do not need to be running and do not even need to be installed.

Some sensors require a recent build of owfs.  If your sensor is not recognized, try updating owfs or compile it yourself.

Here is a minimal configuration to compile owfs:

```
./configure --prefix=/opt/owfs-2.9p8 --disable-parport --disable-zero \
--disable-owtcl --enable-owpython --disable-owphp --disable-owperl \
--disable-owcapi --disable-owmon --disable-owexternal --disable-owserver \
--disable-owftpd --disable-owhttpd --disable-owtap --disable-w1 \
--disable-i2c --disable-ownetlib --disable-owshell --disable-ownet
```