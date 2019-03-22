## How to configure weewx to run in a systemd environment

If your system uses systemd to manage the starting and stopping of daemons, you might want to configure weewx specifically for systemd (`weewx.service`) instead of using the traditional rc script (`/etc/init.d/weewx`).  On most systems with systemd, the weewx rc script will still work.

### The `weewx.service` file

The weewx distribution includes a systemd "unit" file called [`weewx.service`](https://github.com/weewx/weewx/blob/master/util/systemd/weewx.service) that tells systemd how to run weewx.  It looks something like this:

```ini
# systemd configuration for weewx

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

### To run as a non-root user.

You will need to uncomment the last two lines in the [Service] section in the `weewx.service` file.

If you use `weewx` as the `User` and `Group`, then weewx will run as the user `weewx`. If you do this, make sure that the `weewx` user has permission to :
- Write to the weewx database;
- Write to the location for weewx reports (HTML) files;
- and has access to the port your device is connected to You may have to write a udev rule to do this.

First find the `idVendor` and `idProduct` of your weatherstation with `lsusb` command then  add a rules file in `/etc/udev/rules.d/` with this content

```shell
SUBSYSTEM=="usb", ATTR{idVendor}=="your_value", ATTR{idProduct}=="your_value", ACTION=="add", GROUP="weewx", MODE="0664"
```

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