### CSV Service

This is a service for weewx that saves sensor data to a file as comma-separated values.  It can save either LOOP or archive data.

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-csv-0.3.tgz

### How to Install

2.  Run the extension installer:

    ```
setup.py install --extension weewx-csv-x.y.tgz
```

5.  Modify weewx.conf:

    ```
    [CSV]
        filename = /var/tmp/data.csv
```

6. Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Options

_binding_ - The binding determines whether the file will be updated with every LOOP packet or archive record.  Possible values are `loop` or `archive`.  Default is `loop`

_filename_ - The full path of the file.  Default is `/var/tmp/realtime.txt`

_mode_ - Indicates whether to `append` or `overwrite` each time data is written to file.  Default is `append`.

_header_ - Indicates whether to include a header with the name of each field at the beginning of the file.  Default is `True`.