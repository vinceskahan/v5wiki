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

**Note**: logrotate has specific ownership and file access requirements for config files in `/etc/logrotate.d` (including the source file for any linked config files in `/etc/logrotate.d`). logrotate v3.8.x and later requires these config files be owned by `root` or another user with uid 0. The config files must have a 644 permissions mask (read-write by owner, read only by group and others).

2. confirm correct logrotate operation with WeeWX log files:
```
sudo logrotate -d -f /etc/logrotate.d/weewx
```

### Multiple WeeWX instances

Any number of WeeWX instances can be run from a single WeeWX installation, either manually using `weewxd` or via scripts such as the included `weewx-multi`. In such cases it may be useful to direct the WeeWX log output from each instance to a separate file. The instructions for doing so depend on the approach used to run multiple WeeWX instances.

#### Using `weewxd` directly

Consider the case where two WeeWX instances are being run; one for a Davis Vantage Pro station and the other for a Rainwise station using config files `/etc/weewx/vantage.conf` and `/etc/weewx/rainwise.conf` respectively. In this case we will configure the system such that the WeeWX log output for the Davis Vantage Pro station is written to `/var/log/weewx/weewxd-vantage.log` and the log output for the Rainwise station is written to `/var/log/weewx/weewxd-rainwise.log`. 

1. ensure the log output from each instance is separately identified by starting each WeeWX instance like this:
```
sudo weewxd --log-label weewxd-vantage /etc/weewx/vantage.conf
sudo weewxd --log-label weewxd-rainwise /etc/weewx/rainwise.conf
```

2. use a syslog configuration `/etc/rsyslog.d/weewx.conf` like this:
```
$template WEEWX_LOGFILE,"/var/log/weewx/%programname%.log
if $programname startswith 'wee' then ?WEEWX_LOGFILE
if $programname startswith 'wee' then stop
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

Consider the case using the two example WeeWX instances controlled by the `weewx-multi` script used in the [How to run multiple instances of WeeWX](https://github.com/weewx/weewx/wiki/weewx-multi#how-to-run-multiple-instances-of-weewx) wiki page. One instance for a Davis Vantage Pro station known as 'house' and the other for an Acurite station known as 'paddock' using config files `/etc/weewx/house.conf` and `/etc/weewx/paddock.conf` respectively. In this case we will configure the system such that the WeeWX log output for the Davis Vantage Pro station is written to `/var/log/weewx/weewx-house.log` and the log output for the Acurite station is written to `/var/log/weewx/weewx-paddock.log`. 

By virtue of the `--log-label` found in either the `weewx-multi` script or `weewx@.service` unit, the log output for the home and paddock instances is as already separately identified as `weewx-house` and `weewx-paddock` respectively.

1. use a syslog configuration `/etc/rsyslog.d/weewx.conf` like this:
```
$template WEEWX_LOGFILE,"/var/log/weewx/%programname%.log
if $programname startswith 'wee' then ?WEEWX_LOGFILE
if $programname startswith 'wee' then stop
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
  rotate 4
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


### rsyslog configuration examples

```
#
# Configuration options for rsyslog / log handling
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

### logrotate configuration examples

```
/var/log/weewxd.log /var/log/weewx.log {
  weekly
  missingok
  rotate 52
  compress
#  delaycompress # do not compress the most recently rotated file
  copytruncate   # copy the file, then truncate
  notifempty

# on some systems the permissions do not propagate, so force them
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
