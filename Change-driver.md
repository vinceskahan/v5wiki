### How to change the driver in a weewx installation

Sometimes you need to change from one driver to another.  For example, when you install weewx, you might start with the Simulator, then later decide to change to actual hardware, say a Vantage weather station.  This guide shows how to do this.

The driver is specified in the weewx.conf file, specifically by the `station_type` parameter in the `[Station]` section.  There must be a stanza corresponding to the value specified for `station_type`, and that stanza must contain a `driver` parameter that tells weewx where to find the actual code for the driver.

For example, here is the default configuration for the Simulator:

```
[Station]
    ...
    station_type = Simulator

[Simulator]
    # This section is for the weewx weather station simulator

    # The time (in seconds) between LOOP packets.
    loop_interval = 2.5

    # The simulator mode can be either 'simulator' or 'generator'.
    # Real-time simulator. Sleep between each LOOP packet.
    mode = simulator
    # Generator.  Emit LOOP packets as fast as possible (useful for testing).
    #mode = generator

    # The start time. If not specified, the default is to use the present time.
    #start = 2011-01-01 00:00

    # The driver to use:
    driver = weewx.drivers.simulator
```

In weewx 2.x, the default weewx.conf contained a stanza for every one of the supported stations.  In weewx 3.x, one driver stanza is inserted into weewx.conf when you install weewx.

Any number of driver stanzas can be in the weewx.conf file, but only one can be active - the one specified by the `station_type`.

### The manual approach: modify weewx.conf

This approach will work on any weewx installation for both standard and custom drivers.

1. Stop weewx

    ```
sudo /etc/init.d/weewx stop
```

2.  Modify weewx.conf

    For example, to specify the Vantage driver for Davis Vantage Pro2 hardware:

    ```
    [Station]
        ...
        station_type = Vantage

    [Vantage]
        type = serial
        port = /dev/ttyUSB0
        driver = weewx.drivers.vantage
```

3.  Start weewx

### The weewx approach: run setup.py

This approach will work for standard drivers on any weewx installation.

1.  Stop weewx

    ```
sudo /etc/init.d/weewx stop
```
2.  Run setup.py with the configure option

    ```
sudo /home/weewx/setup.py configure
```

3.  Start weewx

This should prompt for the station parameters, including the station type and any options required by the station.

On debian- and redhat-based systems, `wee_setup` is a symlink to the weewx setup.py, so you can simply type `wee_setup` on those systems:

`sudo wee_setup configure`

### The Debian approach: run dpkg

This approach will work for standard drivers on weewx installations that were installed from a .deb package.

1.  Run the reconfigure option to dpkg

    ```
sudo dpkg-reconfigure weewx
```

This should prompt for the station parameters, including the station type and any options required by the station.

### Custom drivers

If you use a custom driver then you must use the manual approach.  A custom driver is any driver that is not in the weewx/drivers folder and that does not implement all of the AbstractConfEditor methods.

For example, if you create your own driver, you should put it in the `user` directory.