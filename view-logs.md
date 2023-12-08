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

On systems that use `systemd`, there is a program called `systemd-journald` that manages some (or all) of the system logging.  In fact, on some systems that use `systemd`, there is no `syslog` - they use `systemd-journald` instead.  Others include both `syslog` and `systemd-journald`.  On systems with `systemd-journald`, you might be able to use the `journalctl` tool to view messages from WeeWX.

Here are some examples:
```
# look at the messages only from the weewx service
sudo journalctl -u weewx
# view all of the message contents, not just the first few words of each line
sudo journalctl -u weewx | more
```

## Make WeeWX save directly to files and skip the system logging

Since version 4, WeeWX uses the Python `logging` module.  This means that you can configure WeeWX to save its messages directly to files, skipping your system's logging mechanism.  The Python `logging` module can also do log rotation, so log files do not grow too big, or so that older log files are deleted automatically.

Using the Python `logging` might be a good choice if you want to save logs to an in-memory partition, in order to minimize writes to disk.

For details, see the [development guide for logging](https://github.com/weewx/weewx/wiki/WeeWX-v4-and-logging).

For even more details, see the [Python logging documentation](https://docs.python.org/3/library/logging.html).

## testing

`logger` for testing
