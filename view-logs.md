This is a guide to viewing WeeWX log messages.

In its default configuration, WeeWX sends log messages to the system logging facility.  This means that the system is responsible for things like ensuring that running multiple instances of `weewxd` do not corrupt the log, and that the system is responsible for rotating log files so that they do not fill up the disks.

There are many different system loggers available.  For example, linux systems have `syslog`, `rsyslog`, `syslog-ng`, and/or `systemd-journald`, BSD systems typically have `syslog` and `newsyslog`. Most of these save the log messages to file, and the system takes care of rotating the log files so that they do not fill up the local storage. Some can be configured to send the logs to a remote logging server, which can make managing multiple machines much easier, and provides security benefits as well.

This document describes some of the configurations you might encounter when you use the system logging facility.

If you want to change the logging configuration, see the Wiki article about [*How to customize logging*](logging)

## syslog/rsyslog

On systems that use `syslog`, when `weewxd` or any service or extension emits a log message, that message goes to the system's logger.  The system then saves the log messages to a file.  Some operating systems use `/var/log/syslog` (Debian, Ubuntu, MacOS, xBSD), while others use `/var/log/messages` (Redhat, Fedora, CentOS, Rocky).  In some configurations, you might see messages from WeeWX in both `/var/log/syslog` and `/var/log/messages`.

You can use standard tools such as `head`, `tail`, `more`, or `less` to view those messages.

Here are some examples:
```
# look at the last 10 messages
sudo tail -10 /var/log/syslog

# look at the first 20 messages, just from weewxd
sudo grep weewxd /var/log/messages | head -20

# watch all of the messages from weewxd as they arrive (ctrl-c to stop)
sudo tail -f /var/log/messages | grep weewxd

# watch the messages from weectl as they arrive, and save them to a file weectl.log
sudo tail -f /var/log/syslog | grep weectl > ~/weectl.log
```

On most modern Linux systems, the `sudo` is required, since the system logs are visible only to the system administration accounts.

## journalctl

On systems that use `systemd`, there is a program called `systemd-journald` that manages some (or all) of the system logging.  On some systems that use `systemd`, there is no `syslog` - they use `systemd-journald` exclusively.  Others include both `syslog` and `systemd-journald`.  On systems with `systemd-journald`, you might be able to use the `journalctl` tool to view messages from WeeWX.

Here are some examples:
```
# look at the messages only from the weewx service
sudo journalctl -u weewx

# view all of the message contents, not just the first few words of each line
sudo journalctl -u weewx | more
```

## How to test your system logger

There is a program called `logger` that you can use to test the behavior of your system's logging facilities.

```
# view all of the options to the logger program
man logger

# send a simple message with priority 'info'
logger -p info "this is my first message"
```

