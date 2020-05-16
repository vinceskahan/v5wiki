### CSV Service

This is a service for weewx that saves sensor data to a file as comma-separated values.  It can save either LOOP or archive data.

### Download

wget -O weewx-csv.zip http://lancet.mit.edu/mwall/projects/weather/releases/weewx-csv-0.10.tgz

### How to Install

1.  Run the extension installer:

```
wee_extension --install weewx-csv.tgz
```

2.  Modify weewx.conf:

```
[CSV]
    filename = /var/tmp/data.csv
```

3. Restart weewx

```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Options

_binding_ - The binding determines whether the file will be updated with every LOOP packet or archive record.  Possible values are `loop` or `archive`.  Default is `loop`

_filename_ - The full path of the file.  Default is `/var/tmp/data.csv`

_mode_ - Indicates whether to `append` or `overwrite` each time data is written to file.  Default is `append`.

_append_datestamp_ - Indicates whether the file should include a timestamp of the form YYYY-mm, so that `/var/tmp/data.csv` becomes `/var/tmp/data-2017-01.csv`, `/var/tmp/data-2017-02.csv`, etc.  Default is `True`.

_datestamp_format_ - The format for the appended datestamp.  For example, `%Y-%m-%d` would result in filenames such as `data-2017-10-01.csv`, with a new file each day.  Default is `%Y-%m`, which results in a new file each month.

_header_ - Indicates whether to include a header with the name of each field at the beginning of the file.  Default is `True`.

_timestamp_format_ - The format for the data timestamp.  For example, `%Y-%m-%d %H:%M:%S` results in a dateTime field like `2017-10-01 14:23:03`.  All times are UTC.  Default is no format, which results in a unix epoch.
