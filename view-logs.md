This is a guide to viewing and managing WeeWX log messages.

In its default configuration, WeeWX sends log messages to the system logging facility, called `syslog`.  This means that the system is responsible for things like ensuring that running multiple instances of `weewxd` do not corrupt the log, and that the system is responsible for rotating log files so that they do not fill up the disks.

There are many different system loggers available.  For example, linux systems have `syslog` and/or `systemd-journald`, BSD systems typically have `syslog` or `newsyslog`. Most of these save the log messages to file, and the system takes care of rotating the log files so that they do not fill up the local storage. Some can be configured to send the logs to a remote logging server, which can make managing multiple machines much easier, and provides security benefits as well.

In many cases, you will want to use the system's logging mechanisms to manage the logs.  However, since WeeWX uses the python `logging` module (since WeeWX V4), you can configure WeeWX to manage its own logs, or configure WeeWX to send its log messages directly to a central log server, or many other options.

This document describes some of the configurations you might encounter on systems that run WeeWX.

## syslog

For every system other than Windows, the default logger for WeeWX is `syslog`.  When `weewxd` or any service or extension emits a log message, that message goes to the system's logger, `syslog`.

## journalctl

Some systems that use `systemd` do not include `syslog` - they use `systemd-journald` instead.  Others include both `syslog` and `systemd-journald`.

## newsyslog

## make weewx save to files and skip syslog

## testing

`logger` for testing
