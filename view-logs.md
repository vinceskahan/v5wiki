This is a guide to viewing and managing WeeWX log messages.

In its default configuration, WeeWX sends log messages to the system logging facility, called `syslog`.  This means that the system is responsible for things like ensuring that running multiple instances of `weewxd` do not corrupt the log, and that the system is responsible for rotating log files so that they do not fill up the disks.

There are many different system loggers available.  For example, linux systems have `syslog` and/or `systemd-journald`, BSD systems typically have `syslog` or `newsyslog`. Most of these save the log messages to file, and the system takes care of rotating the log files so that they do not fill up the local storage. Some can be configured to send the logs to a remote logging server, which can make managing multiple machines much easier, and provides security benefits as well.

In many cases, you will want to use the system's logging mechanisms to manage the logs.  However, since WeeWX uses the python `logging` module (since WeeWX V4), you can configure WeeWX to manage its own logs, or configure WeeWX to send its log messages directly to a central log server, or many other options.

This document describes some of the configurations you might encounter on systems that run WeeWX.

## syslog

The default logger for WeeWX is `syslog`.  When `weewxd` or any service or extension emits a log message, that message goes to the system's logger, `syslog`.  The system then saves the log messages to a file.  Some operating systems use `/var/log/syslog` (Debian, Ubuntu, MacOS, xBSD), while others use `/var/log/messages` (Redhat, Fedora, CentOS, Rocky).  In some configurations, you might see messages from WeeWX in both `/var/log/syslog` and `/var/log/messages`.

If the system saves `syslog` messages to file, then you can use standard tools such as `head`, `tail`, `more`, or `less` to view those messages.

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

Some systems that use `systemd` do not include `syslog` - they use `systemd-journald` instead.  Others include both `syslog` and `systemd-journald`.

## newsyslog

## make weewx save to files and skip syslog

## testing

`logger` for testing
