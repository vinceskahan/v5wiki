This is a guide to viewing WeeWX log messages.

## syslog

For every system other than Windows, the default logger for WeeWX is `syslog`.  When `weewxd` or any service or extension emits a log message, that message goes to the system's logger, `syslog`.



## journalctl

Some systems that use `systemd` do not include `syslog` - they use `systemd-journald` instead.  Others include both `syslog` and `systemd-journald`.

## No screen captures, please!

When posting the log, copy/paste the text into a message, or attach a copy of the log file.  Do not post a screen capture!
