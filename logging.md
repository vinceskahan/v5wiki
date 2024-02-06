This page contains instructions for customizing the logging configuration for WeeWX.

* [Make WeeWX skip the system logging facility](#make-weewx-skip-the-system-logging-facility)
* [Make rsyslog on Linux save WeeWX messages to a separate file](#make-rsyslog-on-linux-save-weewx-logs-separate-from-system)
* [Multiple WeeWX log files for multiple WeeWX processes](#multiple-weewx-log-files)
* [Make syslog on FreeBSD save all WeeWX messages](#make-syslog-on-freebsd-save-all-log-levels)
* [Remote logging](#remote-logging)
* [Example configuration files](#example-configuration-files)


## Make WeeWX skip the system logging facility

Since version 4, WeeWX uses the Python `logging` module.  This means that you can configure WeeWX to save its messages directly to files, skipping your system's logging mechanism.  The Python `logging` module can also do log rotation, so log files do not grow too big, or so that older log files are deleted automatically.

Using the Python `logging` might be a good choice if you run WeeWX in docker, or want to save logs to an in-memory partition in order to minimize writes to disk.

Add this stanza to your WeeWX configuration file, then restart WeeWX.  This will save WeeWX log files to a directory `log` (relative to `WEEWX_ROOT`), rotating once per day at midnight, and retaining 7 days of log files.
```
[Logging]
    LOG_ROOT = log
    [[root]]
        handlers = timed_rotate
    [[handlers]]
        [[[timed_rotate]]]
            filename = {process_name}.log
            level = DEBUG
            formatter = standard
            class = logging.handlers.TimedRotatingFileHandler
            when = midnight
            backupCount = 7
```

For details, see the [development guide for logging](https://github.com/weewx/weewx/wiki/WeeWX-v4-and-logging).

For even more details, see the [Python logging documentation](https://docs.python.org/3/library/logging.html).


## Make rsyslog on Linux save WeeWX logs separate from system

In a default configuration, WeeWX writes log messages to the system logger, so messages from WeeWX are mixed in the system log file with messages from other applications.  With a few changes to the system logging configuration, you can put all WeeWX messages in a separate log file.  In some cases this makes it easier to monitor WeeWX, or multiple instances of WeeWX.

To make this happen you must modify two parts of the system, the logging (typically rsyslog) and the log rotation (typically logrotate).

These instructions assume that WeeWX was installed from DEB or RPM package.  In the commands below, you might have to replace `/etc/weewx` with a path appropriate for your installation.  For example, if WeeWX was installed using `setup.py`, replace `/etc/weewx` with `/home/weewx/util`.  If WeeWX was installed using `pip`, replace `/etc/weewx` with `~/weewx-data`.

### 1. Create a log directory with suitable permissions

For a DEB/RPM install, put the logs in `/var/log/weewx` and set permissions so that the `weewx` user can write.
```
sudo mkdir /var/log/weewx
sudo chmod 775 /var/log/weewx
sudo chown root:weewx /var/log/weewx
```
For a `pip` install, you probably want to keep the logs with the rest of the weewx data, and there is no need to adjust permissions.
```
mkdir ~/weewx-data/log
```

### 2. rsyslog

2a. tell rsyslog to recognize log messages from WeeWX and put them in a separate file:
```
sudo ln -s /etc/weewx/rsyslog.d/weewx.conf /etc/rsyslog.d
```
For a `pip` install, change `/var/log/weewx` to `$HOME/weewx-data/log` before copying the rsyslog config file:
```
cat ~/weewx-data/util/rsyslog.d/weewx.conf | sed "s%/var/log/weewx%$HOME/weewx-data/log%" | sudo tee /etc/rsyslog.d/weewx.conf
```

2b. restart the logging system:
```
sudo systemctl restart rsyslog
```

The file order matters in `rsyslog.d`.  If you find that the WeeWX log messages are going to both the WeeWX log file and the syslog/messages log file, you might have to make the `weewx.conf` appear before the `syslog.conf` defaults.  For example,
```
sudo ln -s /etc/weewx/rsyslog.d/weewx.conf /etc/rsyslog.d/10-weewx.conf
```

### 3. logrotate

3a. tell logrotate to rotate WeeWX log files:
```
sudo ln -s /etc/weewx/logrotate.d/weewx /etc/logrotate.d
```
For a `pip` install, change `/var/log/weewx` to `$HOME/weewx-data/log` before copying the logrotate config file:
```
cat ~/weewx-data/util/logrotate.d/weewx | sed "s%/var/log/weewx%$HOME/weewx-data/log%" | sudo tee /etc/logrotate.d/weewx
```

3b. confirm correct logrotate operation with WeeWX log files:
```
sudo logrotate -d -f /etc/logrotate.d/weewx
```

**Note**: logrotate has specific ownership and file access requirements for config files in `/etc/logrotate.d` (including the source file for any linked config files in `/etc/logrotate.d`). logrotate v3.8.x and later requires these config files be owned by `root` or another user with uid 0. The config files must have a 644 permissions mask (read-write by owner, read only by group and others).


## Multiple WeeWX log files

Any number of WeeWX instances can be run from a single WeeWX installation, either manually using `weewxd` or at system startup. In such cases it may be useful to direct the WeeWX log output from each instance to a separate file. The instructions for doing so depend on the approach used to run multiple WeeWX instances.

Consider the case where two WeeWX instances are being run; one for a Davis Vantage Pro station and the other for a Rainwise station using config files `/etc/weewx/vantage.conf` and `/etc/weewx/rainwise.conf` respectively. In this case we will configure the system such that the WeeWX log output for the Davis Vantage Pro station is written to `/var/log/weewx/weewxd-vantage.log` and the log output for the Rainwise station is written to `/var/log/weewx/weewxd-rainwise.log`. 

1. ensure the WeeWX log directory exists:
```
sudo mkdir -p /var/log/weewx
```

2. use a syslog configuration `/etc/rsyslog.d/weewx.conf` like this:
```
$template WEEWX_LOGFILE,"/var/log/weewx/%programname%.log"
if $programname startswith 'wee' then ?WEEWX_LOGFILE
if $programname startswith 'wee' then stop
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

5. ensure the log output from each instance is separately identified by starting each WeeWX instance using the `--log-label` option, like this:
```
sudo weewxd --log-label weewxd-vantage /etc/weewx/vantage.conf
sudo weewxd --log-label weewxd-rainwise /etc/weewx/rainwise.conf
```

## Make syslog on FreeBSD save all log levels

In its default configuration, FreeBSD saves only log messages that are `NOTICE` or higher.  As a result, the only messages you will see from WeeWX are `ERROR` messages.  This makes it very difficult to diagnose a new installation, since most WeeWX messages are `INFO`.

You can tell FreeBSD to save all log levels, *only* for WeeWX, and save the WeeWX messages to a separate log file.  There are two steps: configure `syslog` (this controls the log level and where log messages are saved) and configure `newsyslog` (this ensures that the log files are rotated regularly).

1. Tell `syslog` to save all log levels from `weewxd` to a log file just for WeeWX.  Put this into the file `/usr/local/etc/syslog.d/weewx.conf`:
```
!weewxd,weectl
*.* /var/log/weewx.log
!*
```
2. Create the initial log file:
```
sudo touch /var/log/weewx.log
sudo chmod 644 /var/log/weewx.log
```
3. Restart `syslog`:
```
sudo service syslogd reload
```

4. Tell `newsyslog` to rotate the WeeWX log file regularly.  Put this into the file `/usr/local/etc/newsyslog.conf.d/weewx.conf`:
```
/var/log/weewx.log 644  5  *  $D0
```


## Remote logging

Sometimes it is useful to send WeeWX log messages to a different server.  This can be particularly useful to eliminate the writes to local disk.  To do this, specify the remote server in the syslog configuration on the machine running WeeWX.

Here is an example of how to do this, with a logging host called `blackhole`.

Put something like this into the file `/etc/rsyslog.d/send-to-blackhole.conf` on the machine running WeeWX:
```
$ActionQueueFileName blackhole
$ActionQueueMaxDiskSpace 1g  
$ActionQueueSaveOnShutdown on
$ActionQueueType LinkedList   
$ActionResumeRetryCount -1
*.* @@blackhole:514
```

On the remote server `blackhole`, make syslog receive messages from other hosts.  Put something like this into the file `/etc/rsyslog.d/receive-logs.conf` on `blackhole`.  If a firewall is running on `blackhole`, be sure to enable incoming TCP/UDP traffic on port 514.
```
# Receive logs using either tcp or udp

$ModLoad imudp
$UDPServerRun 514

$ModLoad imtcp
$InputTCPServerRun 514

# Remote logs go into a separate directory for each client. Use
# standard linux log name patterns.

$template DYNkernel,"/data/rsyslog/%HOSTNAME%/kernel"
$template DYNmessages,"/data/rsyslog/%HOSTNAME%/messages"
$template DYNsecure,"/data/rsyslog/%HOSTNAME%/secure"
$template DYNmaillog,"/data/rsyslog/%HOSTNAME%/maillog"
$template DYNcron,"/data/rsyslog/%HOSTNAME%/cron"
$template DYNspooler,"/data/rsyslog/%HOSTNAME%/spooler"
$template DYNboot,"/data/rsyslog/%HOSTNAME%/boot.log"
$template DYNsystemd,"/data/rsyslog/%HOSTNAME%/systemd"
$template DYNjournald,"/data/rsyslog/%HOSTNAME%/journald"
$template DYNapp,"/data/rsyslog/%HOSTNAME%/%PROGRAMNAME%.log"

# save all of the logs to local storage
if $syslogfacility-text == 'kern' then ?DYNkernel
else if $syslogfacility-text == 'authpriv' then ?DYNsecure
else if $syslogfacility-text == 'mail' then -?DYNmaillog
else if $syslogfacility-text == 'cron' then ?DYNcron
else if $syslogfacility-text == 'uucp' then ?DYNspooler
else if $syslogfacility-text == 'news' then ?DYNspooler
else if $syslogfacility-text == 'local7' then ?DYNboot
else if $programname == 'systemd' then ?DYNsystemd
else if $programname == 'systemd-logind' then ?DYNsystemd
else if $programname startswith 'journal' then ?DYNjournald
else if $programname == 'nginx' then ?DYNapp
else if $programname == 'httpd' then ?DYNapp
else if $programname == 'influxd' then ?DYNapp
else if $programname == 'grafana-server' then ?DYNapp
else ?DYNmessages

# stop here for everything other than the syslog server
:hostname,!isequal,"blackhole" stop
```

## Example configuration files

The `syslog` and `logrotate` utilities have been around for a long time. Although they are well-documented and reliable, there have been changes over the years, and there are variations in how they are configured between different operating systems and operating system versions.  This section includes sample `syslog` and `logrotate` configuration files with comments and examples of many of these variations.

### syslog configuration

```
#
# Configuration options for rsyslog
#
# weewxd.log - messages from the weewx daemon
# weewx.log  - messages from the weewx utilities
#
# This configuration file defaults to a traditional rsyslog configuration.
# However, it also includes examples for various other types of rsyslog
# configurations that might be better suited to specific rsyslog versions
# and/or operating system specifics.

# Default to old-style Property-Based Filters syntax for rsyslog
:programname,isequal,"weewxd" /var/log/weewxd.log
:programname,isequal,"weewxd" stop
:programname,startswith,"wee" /var/log/weewx.log
:programname,startswith,"wee" stop


## EXAMPLE 1:
## Rsyslog 'RainerScript' full syntax with multiple conditions
## 'RainerScript' has replaced the BSD syntax, but both are supported.
## Basic if...else support since v5, fully supported since v6
##
## The conditionals for 'journal' handle the case where the systemd journal
## is messing around with logging.  In that case, the programname is journal,
## and the log message will start with the actual programname.
##
#if $programname == "weewxd" or ($programname == "journal" and $msg startswith "weewxd") then {
#    /var/log/weewxd.log
#    stop
#}
#if $programname startswith "wee" or ($programname == "journal" and $msg startswith "wee") then {
#    /var/log/weewx.log
#    stop
#}


## EXAMPLE 2:
## A more basic 'RainerScript' syntax
##
#if $programname == 'weewxd' then /var/log/weewxd.log
#if $programname == 'weewxd' then stop
#if $programname startswith 'wee' then /var/log/weewx.log
#if $programname startswith 'wee' then stop

## Same example, but deal with systemd/journald
#if $programname == 'journal' and $msg startswith 'weewxd' then /var/log/weewxd.log
#if $programname == 'journal' and $msg startswith 'weewxd' then stop
#if $programname == 'journal' and $msg startswith 'wee' then /var/log/weewx.log
#if $programname == 'journal' and $msg startswith 'wee' then stop


## EXAMPLE 3 - deprecated:
## The tilde "~" discard action is deprecated, but supported, since v6
##
#:programname,isequal,"weewxd" /var/log/weewxd.log
#:programname,isequal,"weewxd" ~
#:programname,startswith,"wee" /var/log/weewx.log
#:programname,startswith,"wee" ~
```

### logrotate configuration

```
/var/log/weewxd.log /var/log/weewx.log {
  weekly
  missingok
  rotate 52
  compress
  delaycompress  # do not compress the most recently rotated file
  copytruncate   # copy the file, then truncate
  notifempty

# on some systems the permissions must be specified explicitly
#  create 644 root adm    # default user/group on Debian
#  create 644 syslog adm  # default user/group on Ubuntu
#  create 644 root root   # default user/group on Redhat

## The default logrotate behavior is to use "create" instead of "copytruncate".
## However, if you do not use "copytruncate", then on some systems rsyslog
## must be reloaded, or weewx must be restarted, otherwise the weewx log
## messages will go to the previous log file, not the newly rotated one.
##
## The following example shows how to use the 'sharedscripts' and 'postrotate'
## options to restart rsyslog.
##
## The command to restart rsyslog depends on the operating system - some
## examples are given below.  In the postrotate section, uncomment only one
## of the commands.  If you choose this approach, be sure to test!
##
# sharedscripts  # Run the command only _once_, not pr file.
# postrotate     # Run the script after rotation is done.
### On systems with SystemD
#   systemctl kill -s HUP rsyslog.service
### On systems that have "pgrep / pkill"
#   pkill -HUP rsyslog
### If SysV init scripts are available
#   /etc/init.d/rsyslog stop
#   /etc/init.d/rsyslog start
### SysV where reload is available
#   /etc/init.d/rsyslog reload > /dev/null
### Ubuntu systems with upstart
#   service rsyslog restart > /dev/null
### Some (older?) RedHat/Fedora systems have the "reload" command
#   reload rsyslog > /dev/null 2>&1
### Debian systems might have this command available
#   invoke-rc.d rsyslog reload > /dev/null
# endscript
}
```
