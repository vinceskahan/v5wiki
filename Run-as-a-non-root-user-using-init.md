> Note!
>
> As of WeeWX 5.0.1, WeeWX runs as a non-root user for DEB/RPM installations as well as pip installations.  The following applies only to older WeeWX installations.

By default, WeeWX runs as root.  This guide illustrates how to run WeeWX as a non-root user, using `init` style "rc" scripts. An alternative way, usually simpler, is to use systemd "unit files". For information on the latter, see the companion article [How to configure WeeWX to run under systemd](systemd).


1.  Create the user account to be used to run WeeWX, if it has not been done already
   
    ```
    shell
    sudo useradd wxuser
    ```

2.  Give serial/usb access to the user with a "udev" rule. There are many ways to do this, but the following usually works for a USB device. First, figure out the vendor and device ID by using the `lsusb` command:
    ```
    /home/weewx$ lsusb
    Bus 001 Device 002: ID 8087:8000 Intel Corp. Integrated Rate Matching Hub
    Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
    Bus 003 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
    Bus 002 Device 005: ID 8087:07dc Intel Corp. Bluetooth wireless interface
    <b>Bus 002 Device 003: ID 10c4:ea60 Silicon Labs CP210x UART Bridge</b>
    Bus 002 Device 004: ID 413c:2010 Dell Computer Corp. Keyboard
    Bus 002 Device 002: ID 413c:1003 Dell Computer Corp. Keyboard Hub
    Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
    ```
    From this, we can identify that the station is on Bus 2, Device 5, the vendor ID is `10c4`, and the product id is 
    `ea60`. 
 
3.  Create the dev rule file:
    ```
    /etc/udev/rules.d/weewx.rules
    ```
    with the following contents:
    ```
    ini    
    SUBSYSTEM=="usb", ATTR{idVendor}=="10c4", ATTR{idProduct}=="ea60", MODE="0664", GROUP="wxuser"
    ```
    This tells udev to look for a vendor/product ID combination of `10cr/ea60`. When it finds it, set its read/write
    mode to `0664` and assigns it to user group `wxuser`. This will allow user `wxuser` to access it.

The next steps depend on whether you used the `setup.py` install method or a package installer to install WeeWX.

## Installed using `setup.py`

This assumes that WeeWX has been installed to `/home/weewx` using `setup.py` and works properly when run as the root user.

1.  Stop WeeWX if it is running:
    ```
    sudo /etc/init.d/weewx stop
    ````

2.  In the startup script file, `/etc/init.d/weewx`, specify the user, and a location for the process ID (PID) file where that user has write privileges, e.g.:
    ```
    WEEWX_USER=wxuser:wxuser
    PIDFILE=/home/weewx/run/$NAME.pid
    ```

3.  Create the directory where the PID file will reside
    ```
    sudo mkdir /home/weewx/run
    ```

4.  Give the user ownership of all WeeWX files:
    ```
    sudo chown -R wxuser:wxuser /home/weewx
    ```

5.  Start WeeWX:
    ```
    sudo /etc/init.d/weewx start
    ```

## Installed using a Debian package

This assumes that WeeWX has been installed using a package installer, e.g.: 
```
apt install weewx
```

1.  First, see if `weewxd` was actually started from an `rc` script and whether it is running by using the systemd command `systemctl`:
    ```
    shell
    sudo systemctl status weewx
    ```
    The output of the _status_ command should show you, under "Loaded:", that it is loaded from `/etc/init.d/weewx`

2.  Stop WeeWX if it is running:
    ```
    shell
    sudo /etc/init.d/weewx start
    ```

3.  Rather than edit the startup script directly (and risk losing changes each update), we override parameters by creating a file `/etc/default/weewx` (if it doesn't exist already), in which we specify the user, and a location for the process ID (PID) file. For example:
    ```
    ini
    WEEWX_USER=wxuser:wxuser
    WEEWX_PID=/tmp/weewx.pid
    ```
    In this example, we are putting the process ID in `/tmp/weewx.pid`. Almost any place will work, provided that user `wxuser` has write permission.

4.  Give the user ownership of all sqlite DB files
    ```
    shell
    sudo chown -R wxuser:wxuser /var/lib/weewx
    ```

5.  Give the user ownership of where the HTML files will go:
    ```
    shell
    sudo chown -R wxuser:wxuser /var/www/html/weewx
    ```

6.  Start WeeWX and check status
    ```
    shell
    /etc/init.d/weewx status
    /etc/init.d/weewx status
    ``` 

### Debian 10

With the upgrade to Buster the system was changed in a way that pidfiles owned by non-root users are not trusted and you cannot stop or restart the weewx service. The solution is to edit the init script `/etc/init.d/weewx`. In the functions `do_stop()` and `do_restart()`, edit the arguments to the `start-stop-daemon` command. For example, edit the `do_stop()` function so that instead of:
    ```
    start-stop-daemon --stop --pidfile $PIDFILE
    ```
    it reads
    ```
    start-stop-daemon --stop --pidfile $PIDFILE --user $WEEWX_USER
    ```
    and likewise for the `do_restart()` function.

There is a further complexity: the `--user` parameter cannot take the form of `user:group` so the easiest way is to simply remove the ":wxuser" part from the definition of `WEEWX_USER` in `\etc\default\weewx`. This will not be a problem if the group name is the default for the WeeWX user, as the default group is applied automatically. If you require a different group then modify the init.d script accordingly with a suitable `WEEWX_GROUP` parameter.
