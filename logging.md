## Save weewx log messages to a separate file

In a default configuration, weewx writes log messages to the system log.  With a few changes to the system logging configuration, you can put put all of the weewx messages in a separate log file.  In some cases this makes it easier to monitor weewx, or multiple instances of weewx.

To make this happen you must modify two parts of the system, the logging (typically rsyslog) and the log rotation (typically logrotate).

### rsyslog

1. tell rsyslog to recognize log messages from weewx and put them in a separate file
    ```
sudo ln -s /etc/weewx/rsyslog.d/weewx.conf /etc/rsyslog.d
```

2. restart the logging system
    ```
sudo /etc/init.d/rsyslog stop
sudo /etc/init.d/rsyslog start
```

### logrotate

1. tell logrotate to rotate weewx log files
    ```
sudo ln -s /etc/weewx/logrotate.d/weewx /etc/logrotate.d
```

### Multiple instances of weewx

If you run multiple instances of weewx, start each with a different log identifier, then configure syslog to send output from each to a separate file.

For example, if you run one instance for a vantage station and another instance for a rainwise station, start each instance like this:

```
sudo weewxd --log-label vantage /etc/weewx/vantage.conf
sudo weewxd --log-label rainwise /etc/weewx/rainwise.conf
```

Use a syslog configuration /etc/rsyslog.d/weewx.conf like this:

```
:programname,startswith,"wee_" /var/log/weewx/weewx.log
:programname,startswith,"wee_" ~
:programname,isequal,"weewx-vantage" /var/log/weewx/vantage.log
:programname,isequal,"weewx-vantage" ~
:programname,isequal,"weewx-rainwise" /var/log/weewx/rainwise.log
:programname,isequal,"weewx-rainwise" ~
```

Be sure that the weewx logging directory exists:
```
sudo mkdir /var/log/weewx
```

Use a logrotate configuration /etc/logrotate.d/weewx something like this:
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

### Remote logging

Sometimes it is useful to send weewx log messages to a different server.  This can be particularly useful to eliminate the writes to local disk.