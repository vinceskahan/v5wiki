### How to change the driver in a WeeWX installation

Sometimes you need to change from one driver to another.  For example, when you
install WeeWX, you might start with the Simulator, then later decide to change
to actual hardware, say a Vantage weather station.  This guide shows how to do
this.

The driver is specified in the `weewx.conf` file, specifically by the
`station_type` parameter in the `[Station]` section.  There must be a stanza
corresponding to the value specified for `station_type`, and that stanza must
contain a `driver` parameter that tells WeeWX where to find the actual code for
the driver.

For example, here is the default configuration for the Simulator:

```
[Station]
    ...
    station_type = Simulator

[Simulator]
    # This section is for the WeeWX weather station simulator

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

In WeeWX 2.x, the default `weewx.conf` contained a stanza for every one of the
supported stations.  In WeeWX 3.x, one driver stanza is inserted into `weewx.conf`
when you install WeeWX.

Any number of driver stanzas can be in the `weewx.conf` file, but only one can
be active - the one specified by the `station_type`.

### The manual approach: modify `weewx.conf`

This approach will work on any WeeWX installation for both standard and custom
drivers.

1. Stop weewx

    ```shell
    sudo systemctl stop weewx
    ```

2.  Modify `weewx.conf`

    For example, to specify the Vantage driver for Davis Vantage Pro2 hardware:

    ```ini
        [Station]
            ...
            station_type = Vantage
    
        [Vantage]
            type = serial
            port = /dev/ttyUSB0
            driver = weewx.drivers.vantage
    ```

3.  Start weewx

    ```shell
    sudo systemctl start weewx
    ```

### The WeeWX approach: use `weectl station`

This approach will work on any WeeWX installation for standard drivers and/or drivers in the user directory.

1. Stop weewx

    ```shell
    sudo systemctl stop weewx
    ```

2. Run `weectl station` with the `reconfigure` action. This will prompt for
  new station options, including the driver.

    ```shell
    weectl station reconfigure
    ```

3. Start weewx

    ```shell
    sudo systemctl start weewx
    ```

### The Debian approach: run dpkg

This approach will work for standard drivers on WeeWX installations that were installed from a .deb package.

1.  Run the reconfigure option to dpkg

    ```shell
    sudo dpkg-reconfigure weewx
    ```

This should prompt for the station parameters, including the station type and any options required by the station.

### Custom drivers

If you use a custom driver then you must use the manual approach.  A custom
driver is any driver that does not implement all of the `AbstractConfEditor`
methods.

If you develop your own driver, you should put it in the `user` directory then
manually configure `weewx.conf`.