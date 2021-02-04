## Using logwatch to monitor WeeWX log messages

logwatch is a system for summarizing log files.  It matches patterns in log messages to create reports about the contents of log files for various system services such as mail, disk usage, software installations, etc.  This is a guide to make logwatch understand WeeWX log messages so that logwatch will report statistics such as the number of WeeWX uploads to weather underground, number of failed upload attempts over a month/week/day, etc.

There are three files required to make logwatch understand WeeWX (or any new service).  The first is the service configuration file (services/weewx.conf), the second is the logfile configuration file (logfiles/weewx.conf), and the third is the script that generates the WeeWX report (scripts/services/weewx).

These instructions assume that WeeWX was installed from DEB or RPM package.  If WeeWX was installed using setup.py to `/home/weewx`, the WeeWX logwatch files will be located in `/home/weewx/util/logwatch` instead of `/etc/weewx`, so replace `/etc/weewx` with `/home/weewx/util` in the commands below.  In either case, you can copy or symlink the files.

1. install logwatch
```
apt-get install logwatch
```

2. tell logwatch what the weewx service is
```
ln -s /etc/weewx/logwatch/conf/services/weewx.conf /etc/logwatch/conf/services
```

3. tell logwatch which log files WeeWX uses
```
ln -s /etc/weewx/logwatch/conf/logfiles/weewx.conf /etc/logwatch/conf/logfiles
```

4. tell logwatch how to interpret WeeWX log messages
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

If you have configured rsyslog to put WeeWX logfiles into a location other than /var/log/syslog, modify the logfiles/weewx.conf file.

To look for different log messages or adjust the report formatting, modify the perl script /etc/logwatch/scripts/services/weewx

## What does it look like?

The WeeWX logwatch script summarizes all of the known WeeWX messages and provides a summary such as number of images generated, number of upload failures, etc.  Any unrecognized log messages, such as exceptions or other failures, are listed after the summary.  

Here is an example of the output from a daily run of logwatch (thank you vince :)

```
################### Logwatch 7.4.0 (05/02/12) ####################
        Processing Initiated: Wed Apr 13 08:52:20 2016
        Date Range Processed: yesterday
                              ( 2016-Apr-12 )
                              Period is day.
        Detail Level of Output: 0
        Type of Output/Format: stdout / text
        Logfiles for Host: debian
 ##################################################################

 --------------------- weewx Begin ------------------------

 average station clock skew: 2.955
   min: 0 max: 4.99 samples: 6

 counts:
   archive: records added                          2304
   engine: garbage collected                      19990
   filegenerator: files generated                 10656
   forecast: downloads                               16
   forecast: prunings                                17
   forecast: records generated                     2235
   genimages: images generated                     9305
   restful: records published                       720

 forecast downloads:
   NWS                                                8
   WU                                                 8

 forecast prunings:
   NWS                                                8
   WU                                                 8
   Zambretti                                          1

 forecast records generated:
   NWS                                              314
   WU                                              1920
   Zambretti                                          1

 uploads:
   CWOP                                             144
   PWSWeather                                       288
   Wunderground-PWS                                 288


 unmatched lines:
   Apr 12 07:35:15 debian weewx[13199]: wxdata: archive record, delta 15.9023060799
   Apr 12 07:36:34 debian weewx[13199]: imageStackedWindRose: Generated 1 images in 0.42 seconds
   Apr 12 07:36:43 debian weewx[13199]: reportengine: Caught unrecoverable exception in generator weewx.reportengine.RsyncGenerator
   Apr 12 07:36:43 debian weewx[13199]:         ****  global name 'log_success' is not defined
   Apr 12 07:36:43 debian weewx[13199]:         ****  Traceback (most recent call last):
   Apr 12 07:36:43 debian weewx[13199]:         ****    File "/home/weewx/bin/weewx/reportengine.py", line 140, in run
   Apr 12 07:36:43 debian weewx[13199]:         ****      obj.start()
   Apr 12 07:36:43 debian weewx[13199]:         ****    File "/home/weewx/bin/weewx/reportengine.py", line 170, in start
   Apr 12 07:36:43 debian weewx[13199]:         ****      self.run()
   Apr 12 07:36:43 debian weewx[13199]:         ****    File "/home/weewx/bin/weewx/reportengine.py", line 263, in run
...
```