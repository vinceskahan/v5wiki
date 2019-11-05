WeeWX writes to disk for the following:

* Images and HTML files are generated every archive interval
* Messages in system log files
* Meteorological data are saved to sqlite database

Most of the writes are due to report generation.  Reports are written once per archive interval - every 5 minutes on a typical WeeWX installation.  For the StandardReport this means about 600K written per archive interval.  WeeWX writes to the system log file at least once per archive interval, which amounts to about 10K written per archive interval.  The smallest writes happen each archive interval when WeeWX writes to the sqlite database.

To eliminate writes, do the following:

* Save reports to a temporary file system (tmpfs or ramdisk)
* Save system logs to a temporary file system or remote syslog server
* Save meteorological data to a remote mysql server
* Disable use of swap storage

The following recipes are for a Debian-like linux.  Other operating systems have similar capabilities, but the implementation details vary.

## Save reports to a temporary file system

1) Add an entry to fstab

```
    echo "weewx_reports /var/weewx/reports tmpfs size=20M,noexec,nosuid,nodev 0 0" | sudo tee -a /etc/fstab
```

2) Manually mount the new file system

```
    sudo mkdir -p /var/weewx/reports
    sudo mount -a
```

3) Tell WeeWX to use the new file system for reports

```
    sudo sed -i -e 's%HTML_ROOT =.*%HTML_ROOT = /var/weewx/reports%' /home/weewx/weewx.conf
```

4) Restart WeeWX

```
    sudo /etc/init.d/weewx stop
    sudo /etc/init.d/weewx start
```

5) Tell the web server where to find the reports

```
    sudo ln -s /var/weewx/reports /var/www/html/weewx
```
create a file in /etc/apache2/sites-available named weewx.conf with the following contents:

```
Alias /weewx "/var/weewx/reports"
<Directory "/var/weewx/reports">
  Options Indexes Includes FollowSymLinks
  AllowOverride None
  Require all granted
</Directory>
```
then enter the following at the shell prompt to enable the new site and restart the web server:

```
sudo a2ensite
weewx
sudo systemctl reload apache2
```
## Save log files to RAMdisk (option 1)

Turn off logging to start:

`sudo /etc/init.d/rsyslog stop`

Edit fstab to create a location for log files and other runtime files, adjusting size as needed (locations not specifically sized will share available memory):

```
# /run, /var/run, /run/lock, /var/run/lock will be automatically created by default - tmpfs
tmpfs		/tmp			tmpfs	defaults,noatime,nodev,nosuid,noexec,mode=0755,size=10M	    0	0
tmpfs		/var/tmp		tmpfs	defaults,noatime,nodev,nosuid,noexec,mode=0755,size=2M	    0	0
tmpfs		/var/lock		tmpfs	defaults,noatime,nodev,nosuid,noexec,mode=0755              0	0
weewx_reports	/var/weewx/reports      tmpfs   defaults,nodev,nosuid,mode=0755,size=5M                     0   0
tmpfs		/var/log	        tmpfs	defaults,noatime,nodev,nosuid,noexec,mode=0755              0   0
```
Note: Use noatime parameter for EXT4 file systems.

Optional: Remove existing log and temporary storage (alternatively, delete contents to free up storage):

```
sudo rm -Rf /tmp/*
sudo rm -Rf /var/log/*
sudo rm /var/tmp/*
```

Mount new storage and resume logging:

```
sudo mount -a
sudo /etc/init.d/rsyslog start
```

The Apache web server will not work after this until temporary storage for Apache logs is recreated in memory. Create a bash script file named initalize-apache-logs with the following content:

```
#!/bin/bash
sudo mkdir /var/log/apache2
sudo /etc/init.d/apache2 restart
```

Make it an executable file:

`sudo chmod +x initalize-apache-logs`

Finally, ensure that this run whenever the system is rebooted:

`sudo crontab -e`

Add this line at the end:

`@reboot /path/initialize-apache-log`

where path is /home/pi/scripts or whatever is appropriate.

Next, adjust control of logging by `rsyslog`. WeeWX already redirects logs to a dedicated log file: `/etc/weewx/rsyslog.d/weewx.conf` using an `rsyslog` configuration file. You may modify but shouldn't need to:

```
:programname,isequal,"weewx" /var/log/weewx/weewx.log :programname,isequal,"weewx" ~
:programname,startswith,"wee_" /var/log/weewx/weewx.log :programname,startswith,"wee_" ~
```
This gathers logs from weewx and programs beginning with "wee_" such as wee_device. The file is stored in one location and used in another. To activate it and restart logging using it:

```
sudo ln -sf /etc/weewx/rsyslog.d/weewx.conf /etc/rsyslog.d
sudo /etc/init.d/rsyslog restart
```
Finally, a similar adjustment is needed in respect of another configuration that regulates logging retention time and other parameters. WeeWX includes a default `logrotate` configuration file for this: `/etc/weewx/logrotate.d/weewx`.

It should contain:

```
/var/log/weewx.log {
  weekly
  missingok
  rotate 52
  compress
  delaycompress
  notifempty

# on some older systems the permissions do not propagate, so force them
# debian uses root:adm
#  create 644 root adm
# ubuntu uses syslog:adm
#  create 644 syslog adm

# on some older systems rsyslog must be restarted to send output to right file
#  sharedscripts
#  postrotate
# standard way of invoking rc scripts
#    /etc/init.d/rsyslog stop
#    /etc/init.d/rsyslog start
# on some systems a reload will work
#    /etc/init.d/rsyslog reload > /dev/null
# some ubuntu systems use upstart
#    service rsyslog restart > /dev/null
# some redhat/fedora systems have their own way
#    reload rsyslog > /dev/null 2>&1
# some debian systems do it this way
#    invoke-rc.d rsyslog reload > /dev/null
  endscript
```

You may find information on possible configuration changes [here](http://wiki.rsyslog.com/index.php/Main_Page). To activate it and restart logging using these settings:

```
sudo ln -sf /etc/weewx/logrotate.d/weewx /etc/logrotate.d
sudo /etc/init.d/rsyslog restart
```
Note: both of these configuration files need to have correct permission settings of 0644. If necessary:

`sudo chmod 644 weewx`

## Save log files to remote syslog server (option 2)

1) Configure a machine, loghost.example.com, to receive log files.  Modify /etc/rsyslog.conf with something like this:

```
    $ModLoad imuxsock 
    $ModLoad imklog
    $ModLoad imudp
    $UDPServerRun 514
    $template FILENAME,"/var/log/%fromhost-ip%/syslog.log"
    *.* ?FILENAME
```

2) Restart the rsyslog daemon on the log server

```
    sudo /etc/init.d/rsyslog stop
    sudo /etc/init.d/rsyslog start
```

3) Configure the WeeWX computer to send log files.  Modify `/etc/rsyslog.conf` with this:

```
    *.* @loghost.example.com
```
This will work but is considered obsolete. You can cut and paste and edit from the following (the rest is largely standard across Linux distributions):

```
# this is the simplest forwarding action:
*.* action(type="omfwd" target="192.168.1.X" port="514" protocol="tcp")
# it is equivalent to the following obsolete legacy format line:
*.* @@192.168.1.X:514 # do NOT use this any longer!
# Note: if the remote system is unreachable, processing will
# block here and discard messages after a while

# so a better use is
*.*  action(type="omfwd" target="192.168.1.X" port="514" protocol="tcp"
            action.resumeRetryCount="100"
            queue.type="linkedList" queue.size="10000")
# this will de-couple the sending from the other logging actions,
# and prevent delays when the remote system is not reachable. Also,
# it will try to connect 100 times before it discards messages as
# undeliverable.
```

4) Restart the rsyslog daemon on the WeeWX computer

```
    sudo /etc/init.d/rsyslog stop
    sudo /etc/init.d/rsyslog start
```

For details, see the rsyslog documentation:

http://www.rsyslog.com/sending-messages-to-a-remote-syslog-server/

## Disable use of swap storage

`sudo dphys-swapfile swapoff`


