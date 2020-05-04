## How to configure weewx to run in a systemd environment

This page has been (partially?) updated for weewx version 4.

If your system uses systemd to manage the starting and stopping of daemons, you might want to configure weewx specifically for systemd (`weewx.service`) instead of using the traditional rc script (`/etc/init.d/weewx`).  On most systems with systemd, the weewx rc script will still work and systemd commands can and should be used manage the rc script.
If you want to run weewx as a non-root user then the systemd service route is simpler.

Version 4 comes with even more variations in installation, with a choice between python versions.
The instructions here might apply to your installation, or you might need to adapt them to your situation.
Note, because of the complexity of options, _some _of the configuration parameters (for example in `/etc/default/weewx` in Debian-like systems) are still examined by `/usr/bin/weewxd`, even under this systemd.service configuration.

### The `weewx.service` file in setup.py installations

The weewx distribution includes a systemd "unit" file called [`weewx.service`](https://github.com/weewx/weewx/blob/master/util/systemd/weewx.service) that tells systemd how to run weewx.  It looks something like this:

```ini
# systemd configuration for weewx

[Unit]
Description=weewx weather system
Requires=time-sync.target
After=time-sync.target
RequiresMountsFor=/home
# The following two lines are not in the current distribution version, but should be uncommented and used if you
# have changed the [Service] section to automatically restart the weewx service if it crashes. As noted below,
# this can be particularly useful if weewx has an IP connection to the weather station it is monitoring, since
# transient WiFi network problems are quite common, and may cause weewx to crash.
# StartLimitIntervalSec=100
# StartLimitBurst=5

[Service]
ExecStart=/home/weewx/bin/weewxd --daemon --pidfile=/var/run/weewx.pid /home/weewx/weewx.conf
ExecReload=/bin/kill -HUP $MAINPID
Type=simple
PIDFile=/var/run/weewx.pid

# The following two lines are not in the current distribution version, but may be uncommented and used if you
# want the weewx service to automatically restart if it crashes. This can be particularly useful if weewx
# has an IP connection to the weather station it is monitoring, since transient network problems are quite
# common, and may cause the daemon to crash.
#Restart=on-failure
#RestartSec=10

# See notes; by default weewx will run with root privileges
#User=weewx
#Group=weewx

[Install]
WantedBy=multi-user.target
```

To install this file, put it in the systemd configuration directory as `/etc/systemd/system/weewx.service`

Be sure that the paths in the `ExecStart` parameter match your weewx installation.

### Debian package installation
These instructions apply to installation via apt to standard system directories (rather than /home/weewx).

The weewx package does not include a systemd "unit" file.
You will need to create your own, called something like `weewx.service`.
Use the following as a template and edit according to your requirements:
```ini
# systemd configuration for weewx
[Unit]
Description=weewx weather system
Requires=time-sync.target
After=time-sync.target

# Uncomment the following two line if your database is written using mysql or MariaDB on the same host
#After=mysql.service
#BindsTo=mysql.service

# The following two lines  should be uncommented and used if you
# have enabled Restart=on-failure in the [Service] section below.
# StartLimitIntervalSec=100
# StartLimitBurst=5

[Service]
# The following two lines may be uncommented and used if you
# want the weewx service to automatically restart if it crashes.
# This can be particularly useful if weewx has an IP connection to the weather station
# it is monitoring, since transient network problems are quite
# common, and may cause the daemon to crash.
# Adjust timing according to the typical recovery times in your situation
#Restart=on-failure
#RestartSec=20

# See notes later in this wiki; by default weewx will run with root privileges, so
# comment out the following two lines to run weewx as root.
User=weewx
Group=weewx
# create a runtime directory below /run, because non-root users cannot create a file in /run itself
# This works for root as well as non-root users.
RuntimeDirectory=weewx
PIDFile=/run/weewx/weewx.pid
# setting the preserve option stops systemd deleting the PID file when weewx exits (debugging only)
# RuntimeDirectoryPreserve=yes


ExecStart=/usr/bin/weewxd --daemon --pidfile=/run/weewx/weewx.pid /etc/weewx/weewx.conf
ExecReload=/bin/kill -HUP $MAINPID
Type=forking

[Install]
WantedBy=multi-user.target
```

To install this file, put it in the systemd configuration directory as `/etc/systemd/system/weewx.service`

Be sure that the paths in the `ExecStart` parameter match your weewx installation.

### To run as a non-root user.

You will need to uncomment the last two lines in the [Service] section in the `weewx.service` file.

If you use `weewx` as the `User` and `Group`, then weewx will run as the user `weewx`. If you do this, make sure that the `weewx` user has permission to :
- Write to the weewx database;
- Write to the location for weewx reports (HTML) files;
- and has access to the port your device is connected to You may have to write a udev rule to do this.

First find the `idVendor` and `idProduct` of your weatherstation with `lsusb` command then  add a rules file in `/etc/udev/rules.d/` with this content:

```shell
SUBSYSTEM=="usb", ATTR{idVendor}=="your_value", ATTR{idProduct}=="your_value", ACTION=="add", GROUP="weewx", MODE="0664"
```
Name the udev rules file something descriptive, such as an abbreviation of your weatherstation model or just weewx.rules, a la `/etc/udev/rules.d/weewx.rules` (extension must be .rules and filename should be simple, no spaces or special characters other than '-' and/or '_' and should not contain more than one period '.').

### Starting and stopping weewx

To start weewx:

    sudo systemctl start weewx

To stop weewx:

    sudo systemctl stop weewx

These commands should work whether you use the rc script `/etc/init.d/weewx` or the systemd configuration `/etc/systemd/system/weewx.service`.

If you have only the rc script, you should be able to start/stop weewx using either the systemctl syntax or the tradition rc syntax:

    sudo /etc/init.d/weewx stop
    sudo /etc/init.d/weewx start

It is almost certainly a bad idea to have both the rc script and a `weewx.service` file installed and active. If you wish to use the service you have just created to run weewx, you should stop the daemon and delete the references to it in the rc folders:

    sudo /etc/init.d/weewx stop
    sudo update-rc.d weewx remove
    sudo rm /etc/init.d/weewx

### Enabling and disabling weewx

If you want to prevent weewx from starting up when the system boots, disable it like this:

    sudo systemctl disable weewx

To enable it:

    sudo systemctl enable weewx

Whether it is enabled or disabled, you can still start/stop.