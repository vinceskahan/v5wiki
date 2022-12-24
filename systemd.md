# How to configure WeeWX to run under systemd

If your system uses systemd to manage the starting and stopping of daemons, you might want to configure WeeWX
using a systemd unit file (`weewx.service`), instead of the traditional rc script
(`/etc/init.d/weewx`).  On most systems with systemd, the WeeWX rc script will still work, but
the systemd unit files have a number of advantages:
 - It's simpler to run as a non-root user;
 - Dependencies, particularly on time synchronization, are more easily specified;
 - A more declarative style, making the scripts simpler.

The installation proceeds in three steps.
1. Clean up any old `rc` scripts.
2. Installs the unit file `weewx.service`
3. Enable and run the unit file.

## Clean up old `rc` scripts

1. Stop any running instances of `weewxd` that are using `rc` scripts:

    ```shell
   sudo /etc/init.d/weewx stop
    ```
 
2. If it exists, get rid of you old `/etc/init.d` script:
    ```shell
    sudo rm /etc/init.d/weewx
   ```    

## Install the unit file `weewx.service`

WeeWX offers a systemd "unit" file called
[`weewx.service`](https://github.com/weewx/weewx/blob/master/util/systemd/weewx.service), which
tells systemd how to run WeeWX. How you install it depends on whether you used `setup.py` or a package installer 
to install your system.
There is also the possibility to run multiple instances ([[weewx-multi|weewx-multi]] ) under systemd,
for which [[the setup is described below|systemd#Multi-instance-Systemd]]

The unit file looks something like this:

```ini
# systemd unit configuration file for WeeWX
#
# For information about running WeeWX under systemd,
# be sure to read https://github.com/weewx/weewx/wiki/systemd
#
[Unit]
Description=WeeWX weather system
Documentation=https://weewx.com/docs

Requires=time-sync.target
After=time-sync.target
RequiresMountsFor=/home

[Service]
ExecStart=/home/weewx/bin/weewxd /home/weewx/weewx.conf
StandardOutput=null
# To run as a non-root user, uncomment and set username and group here:
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
ExecStart=/usr/bin/weewxd /etc/weewx/weewx.conf
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

2. You will then need to *enable* your WeeWX unit file, which makes sure it gets run during system startup:
    ```shell
    sudo systemctl enable weewx
    ```

3. Finally, start weewx:
    ```shell
    sudo systemctl start weewx
    ```

## Starting and stopping weewxd

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

## To run as a non-root user.

You will need to uncomment the lines in the `[Service]` section of the `weewx.service` file,
where it refers to `User=` and `Group=`. Add the name of the user you wish to run as. For example,
to run as user `weewx` it would look like this (assuming `setup.py` install method):

<pre>
[Service]
ExecStart=/home/weewx/bin/weewxd /home/weewx/weewx.conf
StandardOutput=null
<b>User=weewx
Group=weewx</b>
</pre>

Make sure that whatever user you choose has the following permissions: 
- Permission to write to the weewx database (if using sqlite);
- Permission to write to the location for weewx reports (HTML) files;
- Permission to access the port your device is connected to. 
 
For the last item, you may have to write a udev rule. Here's an outline of how to do this.
First find the `idVendor` and `idProduct` of your weather station by using the `lsusb` command. For example:

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

Create a rules file called `/etc/udev/rules.d/weewx.rules` and include this content:

```
SUBSYSTEM=="usb", ATTR{idVendor}=="10c4", ATTR{idProduct}=="ea60", ACTION=="add", GROUP="weewx", MODE="0664"
```

Of course, your `idVendor` and `idProduct` identifiers are likely to be different. Adjust as necessary.

Now when your station gets plugged into the USB port, this rule was identify it, then set its group ownership to
`weewx`, with read/write privileges `0664`, allowing the user `weewx` to access it.

## Multi-instance Systemd
The changes required to run what systemd jargon refers to as [instantiated services](http://0pointer.de/blog/projects/instances.html)
are quite simple.  The examples here are based on the simple unit file above and the on example configurations on the weewx-multi page.
1. Start with the same .conf files described in the ([[weewx-multi|weewx-multi]] ) page.
2. No changes are required to /etc/default/weewx, and you do not need /etc/default/weewx-multi
3. Locate your current weewx unit file, `weewx.service` (usually either from /etc/systemd/system, or /lib/systemd/system) and create a copy named `weewx@.service` in /etc/systemd/system.
4. Edit the `weewx@.service` file and insert a "%i" in locations where the `house` or `paddock` identifiers need to appear.  For example, at the start, make each description unique
```
 Description=WeeWX weather system located in the %i
```
In the line that runs the daemon you might use:
```
 ExecStart=/home/weewx/bin/weewxd --log-label weewx-%i /home/weewx/%i.conf
```
If your unit file defines a pid-file then you will need to change that also, so that each instance has a uniquely named file - something like `weewx-%i.pid`
### activating the services
The usual commands are modified by appending the instance identifier to the unit name, so that `weewx` becomes `weewx@house` for example.
```
systemctl stop weewx
systemctl disable weewx

systemctl start weewx@house
systemctl start weewx@paddock
systemctl status weewx@\*
```
The wildcard form of the status request shows both instances.

When everything seems to be working, set them up to start automatically:
```
systemctl enable weewx@house
systemctl enable weewx@paddock
```
### Cleaning up
After everything is working, it would be a good idea to reconfigure the original weewx.conf service back to the simulator.
That way, if the singular weewx service is ever restarted unexpectedly there will not be multiple instances trying to access the same database.
The following command will also help avoid potential problems:
```
systemctl mask weewx
```