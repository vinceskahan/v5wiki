### Cumulus Real-Time

This is an extension to weewx that emits data to a file in the format used by Cumulus realtime.txt.  The format for the realtime.txt file is maintained at the cumulus web site:

http://wiki.sandaysoft.com/a/Realtime.txt

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-crt-0.12.tgz

### How to Install

1.  Run the extension installer:

    ```
setup.py install --extension weewx-crt-x.y.tgz
```

2.  Modify weewx.conf:

    ```
    [CumulusRealTime]
        filename = /var/tmp/realtime.txt
        unit_system = METRIC # options are US, METRIC, METRICWX
```

3.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Verify

The realtime.txt file should be modified on each LOOP.  For some stations the update will happen more often than others.  For example, Vantage sensors update as often as every 2.5 seconds, whereas FineOffset stations never update more often than 48 seconds.

The file should contain something like this:

```
25/01/15 05:53:40 -1.3 80 -4.3 9.8 9.0 225 0.0 0.0 989.7 SW 2 km/h C mb cm
8.2 2.2 3.4 3.4 0.0 20.6 40 -5.8 -0.6 -1.0 05:15 -1.3 05:50 10.6 05:35 18.5
05:35 989.7 05:35 989.3 05:05 3.0.1 0 14.6 -1.3 -4.4 NULL NULL NULL 249
0.0 0 0 0 WSW 384 meter -4.6 0.2 0.0 0
```