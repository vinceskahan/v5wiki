## Save WeeWX log messages to a separate file

In a default configuration, WeeWX writes log messages to the system log.  With a few changes to the system logging configuration, you can put all WeeWX messages in a separate log file.  In some cases this makes it easier to monitor WeeWX, or multiple instances of WeeWX.

To make this happen you must modify two parts of the system, the logging (typically rsyslog) and the log rotation (typically logrotate).

These instructions assume that WeeWX was installed from DEB or RPM package.  If WeeWX was installed using setup.py to `/home/weewx`, replace `/etc/weewx` with `/home/weewx/util` in the commands below.

### rsyslog

1. tell rsyslog to recognize log messages from WeeWX and put them in a separate file:
```
sudo ln -s /etc/weewx/rsyslog.d/weewx.conf /etc/rsyslog.d
```

2. restart the logging system:
```
sudo /etc/init.d/rsyslog stop
sudo /etc/init.d/rsyslog start
```

The file order matters in `rsyslog.d`.  If you find that the WeeWX log messages are going to both the WeeWX log file and the syslog/messages log file, you might have to make the `weewx.conf` appear before the `syslog.conf` defaults.  For example,
```
sudo ln -s /etc/weewx/rsyslog.d/weewx.conf /etc/rsyslog.d/10-weewx.conf
```

### logrotate

1. tell logrotate to rotate WeeWX log files:
```
sudo ln -s /etc/weewx/logrotate.d/weewx /etc/logrotate.d
```

### Multiple WeeWX instances

Any number of WeeWX instances can be run from a single WeeWX installation, either manually using `weewxd` or via scripts such as the included `weewx-multi`. In such cases it may be useful to direct the WeeWX log output from each instance to a separate file. The instructions for doing so depend on the approach used to run multiple WeeWX instances.

#### Using `weewxd`

Consider the case where two WeeWX instances are being run; one for a Davis Vantage Pro station and the other for a Rainwise station using config files `/etc/weewx/vantage.conf` and `/etc/weewx/rainwise.conf` respectively. In this case we will configure the system such that the WeeWX log output for the Davis Vantage Pro station is written to `/var/log/weewx/vantage.log` and the log output for the Rainwise station is written to `/var/log/weewx/rainwise.log`. 

1. ensure the log output from each instance is separately identified by starting each WeeWX instance like this:
```
sudo weewxd --log-label weewx-vantage /etc/weewx/vantage.conf
sudo weewxd --log-label weewx-rainwise /etc/weewx/rainwise.conf
```

2. use a syslog configuration `/etc/rsyslog.d/weewx.conf` like this:
```
:programname,startswith,"wee_" /var/log/weewx/weewx.log
:programname,startswith,"wee_" ~
:programname,isequal,"weewx-vantage" /var/log/weewx/vantage.log
:programname,isequal,"weewx-vantage" ~
:programname,isequal,"weewx-rainwise" /var/log/weewx/rainwise.log
:programname,isequal,"weewx-rainwise" ~
```

3. ensure the WeeWX log directory exists:
```
sudo mkdir /var/log/weewx
```

4. use a logrotate configuration `/etc/logrotate.d/weewx` something like this:
```
/var/log/weewx/*.log {
  weekly
  missingok
  rotate 52
  compress
  delaycompress
  notifempty
}
```

5. restart the logging system:
```
sudo /etc/init.d/rsyslog stop
sudo /etc/init.d/rsyslog start
```


#### Using `weewx-multi`

Consider the case using the two example WeeWX instances controlled by the `weewx-multi` script used in the [How to run multiple instances of WeeWX](https://github.com/weewx/weewx/wiki/weewx-multi#how-to-run-multiple-instances-of-weewx) wiki page. One instance for a Davis Vantage Pro station known as 'home' and the other for an Acurite station known as 'paddock' using config files `/etc/weewx/home.conf` and `/etc/weewx/paddock.conf` respectively. In this case we will configure the system such that the WeeWX log output for the Davis Vantage Pro station is written to `/var/log/weewx/home.log` and the log output for the Acurite station is written to `/var/log/weewx/paddock.log`. 

By virtue of the `weewx-multi` script the log output for the home and paddock instances is as already 
separately identified as `weewx-home` and `weewx-paddock` respectively.

1. use a syslog configuration `/etc/rsyslog.d/weewx.conf` like this:
```
:programname,startswith,"wee_" /var/log/weewx/weewx.log
:programname,startswith,"wee_" ~
:programname,isequal,"weewx-home" /var/log/weewx/home.log
:programname,isequal,"weewx-home" ~
:programname,isequal,"weewx-paddock" /var/log/weewx/paddock.log
:programname,isequal,"weewx-paddock" ~
```

2. ensure the WeeWX log directory exists:
```
sudo mkdir /var/log/weewx
```

3. use a logrotate configuration `/etc/logrotate.d/weewx` something like this:
```
/var/log/weewx/*.log {
  weekly
  missingok
  rotate 52
  compress
  delaycompress
  notifempty
}
```

4. restart the logging system:
```
sudo /etc/init.d/rsyslog stop
sudo /etc/init.d/rsyslog start
```


### Remote logging

Sometimes it is useful to send WeeWX log messages to a different server.  This can be particularly useful to eliminate the writes to local disk.

To do this, specify the remote server in `/etc/rsyslog.conf` on the machine running WeeWX.  Then configure the remote server to accept log messages from the machine running WeeWX.