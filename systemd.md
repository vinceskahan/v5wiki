# How to configure WeeWX to run under systemd

If your system uses systemd to manage the starting and stopping of daemons, you might want to configure WeeWX
using a systemd unit file (`weewx.service`), instead of the traditional rc script
(`/etc/init.d/weewx`).  On most systems with systemd, the WeeWX rc script will still work, but
the systemd unit files have a number of advantages:
 - It's simpler to run as a non-root user;
 - Dependencies, particularly on time synchronization, are more easily specified;
 - A more declarative style, making the scripts simpler.

The installation proceeds in two steps. The first installs the unit file `weewx.service`, the second enables and
runs it and various services.

## Install the unit file

The WeeWX distribution includes a systemd "unit" file called 
[`weewx.service`](https://github.com/weewx/weewx/blob/master/util/systemd/weewx.service), which tells systemd how 
to run WeeWX.  It looks something like this:

```ini
# systemd unit configuration file for WeeWX

[Unit]
Description=WeeWX weather system
Documentation=https://weewx.com/docs
#
# Require that a time synchronization service has started before running me.
#
Requires=time-sync.target
After=time-sync.target
#
# NB: the above only requires that a time sync service be started, not actually synchronized!
# To do that, enable systemd-time-wait-sync.service:
#
#   sudo systemctl enable --now systemd-time-wait-sync.service
#

RequiresMountsFor=/home

# The following two lines are not in the current distribution version, but should be uncommented and used if you
# have changed the [Service] section to automatically restart the weewx service if it crashes. As noted below,
# this can be particularly useful if weewx has an IP connection to the weather station it is monitoring, since
# transient WiFi network problems are quite common, and may cause weewx to crash.
# StartLimitIntervalSec=100
# StartLimitBurst=5

[Service]
ExecStart=/home/weewx/bin/weewxd /home/weewx/weewx.conf
StandardOutput=null
#User=weewx
#Group=weewx

[Install]
WantedBy=multi-user.target
```

### For `setup.py` installations

Installations that used `setup.py` will include a copy of the service file. Copy it into position:

```shell
sudo cp /home/weewx/util/systemd/weewx.service /etc/systemd/system
```

Take a look at it, and make sure that the line that starts with `ExecStart` matches your installation. The default
is
```ini
ExecStart=/home/weewx/bin/weewxd /home/weewx/weewx.conf
```
which should work for most cases.

### For Debian package installations
If you installed using a Debian package installer, the distribution does not include with a copy of 
`weewx.service`. Instead, you will have to download it:

```
cd /etc/systemd/system
sudo wget https://raw.githubusercontent.com/weewx/weewx/master/util/systemd/weewx.service 
```

Look through the file and change the line that starts with `ExecStart` to match your installation. It will
probably end up looking like this:

```
ExecStart=/usr/share/weewx/weewxd /etc/weewx/weewx.conf
```
 
## Enabling and running
Now that the unit file is in position, it is time to enable it. These instructions are the same
regardless of how you installed WeeWX.

1. First, note that the unit file has a dependency on `time-sync.target`. This makes sure that `weewxd` does not get run
before a time synchronization service has started. However, it does not guarantee that the time has actually been
synchronized, only that a time service has started! To guarantee that the time has actually been synchronized,
use this command:

    ```shell
    sudo systemctl enable --now systemd-time-wait-sync.service
    ```

    This service will not finish until the time has been synchronized. 

2. If it exists, get rid of you old `/etc/init.d` script:
    ```shell
    sudo rm /etc/init.d/weewx
   ```    
4. You will then need to *enable* your WeeWX unit file, which makes sure it gets run during system startup:
    ```shell
    sudo systemctl enable weewx
    ```

5. Finally, start weewx:
    ```shell
    sudo systemctl start weewx
    ```

### To run as a non-root user.

You will need to uncomment the lines in the `[Service]` section in the `weewx.service` file,
where it refers to `User=` and `Group=`. Add the name of the user you wish to run as. For example,
to run as user `weewx` it would look like this (assuming `setup.py` install method):

<pre>
[Service]
ExecStart=/home/weewx/bin/weewxd /home/weewx/weewx.conf
StandardOutput=null
<b>User=weewx
Group=weewx</b>
</pre>

Make sure that whatever user you use has the following permissions: 
- Permission to write to the weewx database (if using sqlite);
- Permission to write to the location for weewx reports (HTML) files;
- Permission to access the port your device is connected to. 
 
For the last item, you may have to write a udev rule. First find the `idVendor` and `idProduct` of your
weather station by using the `lsusb` command. For example:

<pre>
/home/weewx$ lsusb
Bus 001 Device 002: ID 8087:8000 Intel Corp. Integrated Rate Matching Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 003 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 002 Device 005: ID 8087:07dc Intel Corp. Bluetooth wireless interface
<b>Bus 002 Device 003: ID 10c4:ea60 Silicon Labs CP210x UART Bridge</b>
Bus 002 Device 004: ID 413c:2010 Dell Computer Corp. Keyboard
Bus 002 Device 002: ID 413c:1003 Dell Computer Corp. Keyboard Hub
Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
</pre>

From this, we can identify that the station is on Bus 2, Device 5. The vendor ID is `10c4` and the product id is 
`ea60`.

Create a rules file called `/etc/udev/rules.d/weewx.rules` with this content:

```
SUBSYSTEM=="usb", ATTR{idVendor}=="10c4", ATTR{idProduct}=="ea60", ACTION=="add", GROUP="weewx", MODE="0664"
```

When your station gets plugged into the USB port, this rule was identify it, then set its group ownership to
`weewx`, with read/write privileges `0664`.


### Starting and stopping WeeWXd

To start weewxd:

    sudo systemctl start weewx

To stop weewxd:

    sudo systemctl stop weewx

To find the status:

    sudo systemctl status weewx

If you want to prevent weewxd from starting up when the system boots, disable it like this:

    sudo systemctl disable weewx

Later, you can re-enable it:

    sudo systemctl enable weewx

Whether it is enabled or disabled, you can still start/stop.