## How to configure weewx to run in a systemd environment

If your system uses systemd to manage the starting and stopping of daemons, you might want to configure weewx specifically for systemd (weewx.service) instead of using the traditional rc script (/etc/init.d/weewx).  On most systems with systemd, the weewx rc script will still work.

### The weewx.service file

The weewx distribution includes a systemd "unit" file called weewx.service that tells systemd how to run weewx.  It looks something like this:

    # systemd configuration to run a single instance of weewx as a daemon
    
    [Unit]
    Description=weewx weather system
    Requires=time-sync.target
    After=time-sync.target
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

To install this file, put it in the systemd configuration directory as /etc/systemd/system/weewx.service

Be sure that the paths in the ExecStart parameter match your weewx installation.  If you use weewx as the User and Group, then be sure that the weewx user has permission to write to the weewx database and the location for weewx reports.

### The weewx.service file to run as non-root (with necessary amendments to the ssytem)

```shell
# systemd configuration for weewx

[Unit]
Description=weewx weather system
Requires=time-sync.target
After=time-sync.target
RequiresMountsFor=/home

[Service]
ExecStart=/usr/bin/weewxd --daemon --pidfile=/var/run/weewx/weewx.pid /etc/weewx/weewx.conf
ExecReload=/bin/kill -HUP $MAINPID
Type=simple
RuntimeDirectory=weewx
PIDFile=/var/run/weewx/weewx.pid
User=weewx
Group=weewx

[Install]
WantedBy=multi-user.target
```
 First find the `idVendor` and `idProduct` of your weatherstation with `lsusb` command then  add a rules file in `/etc/udev/rules.d/` with this content

```shell
SUBSYSTEM=="usb", ATTR{idVendor}=="your_value", ATTR{idProduct}=="your_value", ACTION=="add", GROUP="weewx", MODE="0774"
```
Be sure that the weewx user has permission to write to the weewx database and the location for weewx reports.


### Starting and stopping weewx

To start weewx:

    sudo systemctl start weewx

To stop weewx:

    sudo systemctl stop weewx

These commands should work whether you use the rc script /etc/init.d/weewx or the systemd configuration /etc/systemd/system/weewx.service.

If you have only the rc script, you should be able to start/stop weewx using either the systemctl syntax or the tradition rc syntax:

    sudo /etc/init.d/weewx stop
    sudo /etc/init.d/weewx start

It is probably a bad idea to have both the rc script and the weewx.service file installed.

### Enabling and disabling weewx

If you want to prevent weewx from starting up when the system boots, disable it like this:

    sudo systemctl disable weewx

To enable it:

    sudo systemctl enable weewx

Whether it is enabled or disabled, you can still start/stop.