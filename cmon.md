### Monitor Computer Health

The ComputerMonitor (cmon) is a weewx service that collects CPU load, memory used/available, disk space, and network traffic to a database.  You can then add plots to view the data.

<!--- ### Screenshots
 This has been commented out until the screenshots return
![daycpu](http://lancet.mit.edu/mwall/projects/weather/cmon-daycpu.png )
![dayload](http://lancet.mit.edu/mwall/projects/weather/cmon-dayload.png ) -->

### Download

    wget -O weewx-cmon.zip https://github.com/matthewwall/weewx-cmon/archive/master.zip

### How to Install

1.  Run the extension installer

    ```shell
    wee_extension --install weewx-cmon.zip
    ```

2.  Restart weewx

    ```shell
    sudo /etc/init.d/weewx stop
    sudo /etc/init.d/weewx start
    ```

### Verify

cmon will save data at every archive interval.  Look at the cmon report to see plots.

### Customize

Add plots to other reports by modifying the ImageGenerator section of skin.conf.  For example, daily plots of CPU, load, and memory would look something like this:

```
[ImageGenerator]
    ...
    [[cmon_day_images]]
        data_binding = cmon_binding
        time_length = 86400
        x_label_format = %H:%M
        [[[daycpu]]]
            [[[[cpu_user]]]]
            [[[[cpu_system]]]]
            [[[[cpu_iowait]]]]
            [[[[cpu_irq]]]]
            [[[[cpu_softirq]]]]

        [[[dayload]]]
            [[[[load1]]]]
            [[[[load5]]]]
            [[[[load15]]]]

        [[[daymem]]]
            [[[[mem_total]]]]
            [[[[mem_used]]]]
```

then display in templates like this:

```
<img src="daycpu.png" />
<img src="dayload.png" />
<img src="daymem.png" />
```

### Upgrading from weewx 2.6-2.7

The schema for cmon 0.7 is not backward compatible.  You must either delete any existing cmon database (if you do not care to retain data), or add a column called 'interval' to the existing cmon database (if you want to keep existing data).

### Upgrading from weewx 2.5

The default schema changed from cmon r789 to cmon 0.1.  cmon 0.1 adds the ability to specify the schema in weewx.conf or in bin/user/schemas.py.  cmon r789 defaulted to using a database called computer.sdb.  cmon 0.1 and later uses a database called cmon.sdb.

### Source
https://github.com/matthewwall/weewx-cmon
