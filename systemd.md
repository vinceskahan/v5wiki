## How to configure weewx to run in a systemd environment

If your system uses systemd to manage the starting and stopping of daemons, you might want to configure weewx specifically for systemd (weewx.service) instead of using the traditional rc script (/etc/init.d/weewx).  On most systems with systemd, the weewx rc script will still work.  This page explains how to convert to a systemd-only environment.

### The weewx.service file

The weewx distribution includes a file weewx.service that tells systemd how to run weewx.  It looks something like this:

    # systemd configuration for weewx
    [Unit]
    Description=weewx weather system
    Requires=syslog.service
    Requires=ntp.service
    After=syslog.service
    After=ntp.service
    RequiresMountsFor=/home
    [Service]
    ExecStart=/home/weewx/bin/weewxd --daemon --pidfile=/var/run/weewx.pid /home/weewx/weewx.conf
    ExecReload=/bin/kill -HUP $MAINPID
    Type=simple
    PIDFile=/var/run/weewx.pid
    #User=weewx
    #Group=weewx
    [Install]
    WantedBy=multi-user.target

To install this file, put it in the directory /etc/systemd

### Starting and stopping weewx

To start weewx:

    systemctl start weewx

To stop weewx:

    systemctl stop weewx

These commands should work whether you use the rc script /etc/init.d/weewx or the systemd configuration /etc/systemd/weewx.service.  It is probably a bad idea to have both the rc script and the weewx.service file installed.
