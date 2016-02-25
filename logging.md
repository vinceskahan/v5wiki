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
sudo ln -s /etc/weewx/logrotate.d/weewx
```

### Multiple instances of weewx

If you run multiple instances of weewx, start each with a different log identifier, then configure syslog to send output from each to a separate file.

### Remote logging

Sometimes it is useful to send weewx log messages to a different server.  This can be particularly useful to eliminate the writes to local disk.