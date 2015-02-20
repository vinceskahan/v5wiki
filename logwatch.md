## Using logwatch to monitor weewx log messages

logwatch is a system for summarizing log files.  It matches patterns in log messages to create reports about the contents of log files for various system services such as mail, disk usage, software installations, etc.  This is a guide to make logwatch understand weewx log messages so that logwatch will report statistics such as the number of weewx uploads to weather underground, number of failed upload attempts over a month/week/day, etc.

There are three files required to make logwatch understand weewx (or any new service).  The first is the service configuration file (services/weewx.conf), the second is the logfile configuration file (logfiles/weewx.conf), and the third is the script that generates the weewx report (scripts/services/weewx).

To configure logwatch to recognize weewx, do the following.  This assumes that weewx was installed from debian package.  If you installed using setup.py, then copy the files from the weewx util directory instead of symlinking.

1. install logwatch
    ```
apt-get install logwatch
```

2. tell logwatch what the weewx service is
    ```
ln -s /etc/weewx/logwatch/conf/services/weewx.conf /etc/logwatch/conf/services
```

3. tell logwatch which log files weewx uses
    ```
ln -s /etc/weewx/logwatch/conf/logfiles/weewx.conf /etc/logwatch/conf/logfiles
```

4. tell logwatch how to interpret weewx log messages
    ```
ln -s /etc/weewx/logwatch/scripts/services/weewx /etc/logwatch/scripts/services
```

5. test by requesting a summary from the previous day
    ```
logwatch --service weewx --range yesterday
```

## Examples

This would give you a summary of everything that has happened over the past 60 days:
```
logwatch --service weewx --range '-60 days'
```

or, to see everything from all log files (included gzipped logfiles):
```
logwatch --service weewx --range all
```

The range option understands many variations:
```
   range today
   range yesterday
   range '4 hours ago for that hour'
   range '-3 days'
   range 'since 2 hours ago for those hours'
   range 'between -10 days and -2 days'
   range 'Apr 15, 2005'
   range 'first Monday in May'
   range 'between 4/23/2005 and 4/30/2005'
   range '2005/05/03 10:24:17 for that second'
```
Use the --debug option to see exactly what logwatch is doing.

If you have configured rsyslog to put weewx logfiles into a location other than /var/log/syslog, modify the logfiles/weewx.conf file.

To look for different log messages or adjust the report formatting, modify the perl script /etc/logwatch/scripts/services/weewx