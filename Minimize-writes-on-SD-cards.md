##Minimize writes for systems using SD cards or SSDs##

WeeWX writes to disk for the following:

* Images and HTML files are generated every archive interval
* Messages in system log files
* Meteorological data are saved to sqlite database

Most of the writes are due to report generation.  Reports are written once per archive interval - every 5 minutes on a typical WeeWX installation.  For the StandardReport this means about 600K written per archive interval.  WeeWX writes to the system log file at least once per archive interval, which amounts to about 10K written per archive interval.  The smallest writes happen each archive interval when WeeWX writes to the sqlite database.

To eliminate writes, do the following:

* Save reports to a temporary file system (tmpfs or ramdisk)
* Save system logs to a temporary file system or remote syslog server
* Save meteorological data to a remote mysql server

The following recipes are for a Debian-like linux.  Other operating systems have similar capabilities, but the implementation details vary.

## Save reports to a temporary file system

1) Add an entry to fstab

    echo "weewx_reports /var/weewx/reports tmpfs size=20M,noexec,nosuid,nodev 0 0" | sudo tee -a /etc/fstab

2) Manually mount the new file system

    sudo mkdir -p /var/weewx/reports
    sudo mount -a

3) Tell WeeWX to use the new file system for reports

    sudo sed -i -e 's%HTML_ROOT =.*%HTML_ROOT = /var/weewx/reports%' /home/weewx/weewx.conf

4) Restart WeeWX

    sudo /etc/init.d/weewx stop
    sudo /etc/init.d/weewx start

5) Tell the web server where to find the reports

    sudo ln -s /var/weewx/reports /var/www/html/weewx

## Save log files to remote syslog server

1) Configure a machine, loghost.example.com, to receive log files.  Modify /etc/rsyslog.conf with something like this:

    $ModLoad imuxsock 
    $ModLoad imklog
    $ModLoad imudp
    $UDPServerRun 514
    $template FILENAME,"/var/log/%fromhost-ip%/syslog.log"
    *.* ?FILENAME

2) Restart the rsyslog daemon on the log server

    sudo /etc/init.d/rsyslog stop
    sudo /etc/init.d/rsyslog start

3) Configure the WeeWX computer to send log files.  Modify /etc/rsyslog.conf with this:

    *.* @loghost.example.com

4) Restart the rsyslog daemon on the WeeWX computer

    sudo /etc/init.d/rsyslog stop
    sudo /etc/init.d/rsyslog start

For details, see the rsyslog documentation:

http://www.rsyslog.com/sending-messages-to-a-remote-syslog-server/
