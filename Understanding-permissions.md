## Working with permissions in a Unix environment

This is an introduction to how permissions work in a Unix environment, with functional examples that you might encounter with WeeWX.

There are two general classes of users in a Unix environment: (1) privileged and (2) unprivileged.  A privileged user has the ability to do things to the system that affect how the system operates and could break the system if applied incorrectly.  For example, administrative privileges are typically required to upgrade the operating system or to install system software.  An unprivileged user can run software and save data, but only in ways that would not break the system. Usually you login to a computer as an unprivileged user, then you only *escalate* privilege when you do specific, administrative activities.  This helps prevent silly mistakes, and it provides a layer of protection against malicious behavior.

* [Privilege escalation: sudo vs su](#sudo-vs-su)
* [The WeeWX user](#the-weewx-user-and-group)

For each file and directory, there is a set of permissions that define who can read and write that file or directory.  The permissions are defined by `owner`, `group`, and `world`.  This lets you say "only bill can write to file X, but anyone can read it", or "anyone in the `weewx` group can write to this directory, and no one else can even read it".

Since USB and serial devices are also just files (a special kind of file, but still just files), the same permissions system applies to them.

The following sections explain how permissions affect different parts of WeeWX.

* [modifying the configuration file and skins](#modifying-a-configuration-file-or-skin)
* [installing and removing extensions](#installing-an-extension)
* [reading the database](#readingwriting-to-a-database)
* [ownership of the files and directories for reports](#writing-reports)
* [access to USB and/or serial devices](#readingwriting-data-to-a-device)
* [access to network resources](#binding-to-a-network-port)
* [access to the log files](#viewing-the-log)

Sometimes permissions and/or ownership get mixed up, typically when upgrading from a pre-V5 installation, or when you copy files or backups from one machine to another.

* [How to fix permissions](#how-to-fix-permissions)


### The WeeWX user and group

Before V5, WeeWX ran as the user `root`.  When WeeWX runs as the `root` user, runtime permissions are not an issue - `root` has permission to do anything.  However, any user other than `root` would have to become `root` in order to make changes to the configuration.

Starting with V5, WeeWX runs as a non-root user, either the dedicated `weewx` user (DEB/RPM installs), or the user who installed WeeWX (pip installs).  Running as a non-root user is considered best practice - it minimizes the damage to the system should something go awry, and it is more secure against nefarious attacks.  However, it requires that permissions are configured for reading data from the weather station (typically USB or serial devices, or perhaps network interfaces), saving data to the database, and saving data to the files and directories that constitute the reports.

For DEB/RPM installations, the WeeWX configuration files and skins are owned by the `weewx` user.  The files are also in a group called `weewx`, so anyone in the `weewx` group will also have permission to modify the files.  For DEB/RPM installations, the installer puts you into the `weewx` group.  When you are in the `weewx` group, you do not have to `sudo` to modify the WeeWX files in `/etc/weewx`, but you do need `sudo` to start/stop the `weewxd` daemon.

Verify that you are in the `weewx` group using the `groups` command.
```
groups
```

If somehow you were not added to the `weewx` group, you can add yourself.  Note that you must create a new shell/terminal to see the group change.  Or you can log out then log back in.
```
sudo usermod -a -g weewx $USER
```

### sudo vs su

How does an unprivileged user get permission to do system administration things?  There are two approaches: 

1. temporary escalation using `sudo`, or 
2. extended escalation by becoming `root`.  

For temporary escalation, prefix a command with `sudo`.  For example, instead of `nano /etc/weewx/weewx.conf` you would do `sudo nano /etc/weewx/weewx.conf`.  For extended escalation, you login as the `root` user.  For example, you can become root by doing `su` or `su --login`.

Which approach is better?  It depends.  See the [Debian article about sudo](https://wiki.debian.org/sudo/) for pros and cons of each approach.

Beware that `sudo` may not be on your system, depending on how you installed/configured the operating system.  If there is no `sudo`, use your system's package manager to install it.

Note that BSD systems have the command `doas` as well as or instead of `sudo`, depending on how you installed/configured the operating system.

Privilege escalation usually affects you when you need to view or modify a file.  It will also affect you when you try to start and stop certain processes.

System files and directories are usually protected, so that only users with administrative privileges can modify them.  In some cases, administrative privileges are necessary even to *view* them.

Files and directories in your `HOME` directory do not have these restrictions.

Here are some situations where permissions matter when you are using WeeWX.


### Modifying a configuration file or skin

If you installed WeeWX using `pip`, then all of the station settings and skins should be owned by you, so you can simply edit them:
```
nano ~/weewx-data/weewx.conf
```
If you installed WeeWX from a DEB/RPM package, then the station settings and skins are owned by the `weewx` user.  As long as you are in the `weewx` group, you can modify them:
```
nano /etc/weewx/weewx.conf
```

### Installing an extension

You must have read/write permissions on the WeeWX "user" directory in order to install or remove extensions.  If you installed using `pip`, then you should be the owner of the `weewx-data` directory, so you can just invoke `weectl extension` and everything should work. 
```
$ weectl extension install /var/tmp/gw1000-0.3.1.tar.gz
Request to install '/var/tmp/gw1000-0.3.1.tar.gz'
Extracting from tar archive /var/tmp/gw1000-0.3.1.tar.gz
Saving installer file to /home/jackhandy/weewx-data/bin/user/installer/GW1000
Saved configuration dictionary. Backup copy at /home/jackhandy/weewx-data/weewx.conf.20210426101025
Finished installing extension '/var/tmp/gw1000-0.3.1.tar.gz'
```

If you installed using a DEB or RPM package, then the `user` directory will be owned by `weewx`.  The installer *should* have put you into the `weewx` group, so you can just install the extension.

If somehow you are not in the `weewx` group, or the permissions are set incorrectly, you will see permission failure:
```
$ weectl extension install /var/tmp/gw1000-0.3.1.tar.gz
Request to install '/var/tmp/gw1000-0.3.1.tar.gz'
Extracting from tar archive /var/tmp/gw1000-0.3.1.tar.gz
Traceback (most recent call last):
   [...]
PermissionError: [Errno 13] Permission denied: '/etc/weewx/bin/user/gw1000.py'
```


### Reading/writing to a database

In a default configuration, the WeeWX database is world-readable, but writable only by the owner.  So you should be able to read the WeeWX database no matter how you installed WeeWX.  If you see messages about "database locked", then the problem is probably not because of permissions or ownership.

```
# pip install
sqlite3 ~/weewx-data/archive/weewx.sdb

# deb/rpm install
sqlite3 /var/lib/weewx/weewx.sdb
```


### Writing reports

The WeeWX process must have write permission on the report directory, `HTML_ROOT`.  If you put `HTML_ROOT` in a location other than the default, you must ensure that the user running WeeWX has write permission to that location.

For DEB/RPM installations, the default `HTML_ROOT` directory is `/var/www/html/weewx`.  This directory will be created with correct permissions when you install WeeWX.

For `pip` installations, the `HTML_ROOT` directory is typically `weewx-data/public_html` in the home directory of the user running WeeWX.

If you see "no permission" messages in the log when WeeWX is generating reports, check the directory and file permissions.  If the user running WeeWX is not the owner and not in a file/directory group with write permissions, then it will not be able to write reports.
```
ls -la /var/www/html/weewx
```


### Reading/writing data to a device

First of all, most USB and serial devices are accessible to only one process at a time.  For example, if `weewxd` is running and communicating with the device `/dev/ttyUSB0`, then you will not be able to read/write to the device `/dev/ttyUSB0` even if you have sufficient permissions.

By default, only a privileged user can write to USB or serial devices.  If you want someone other than `root` to read/write a USB or serial device, then you must change the permissions on that device.  Usually it is best to define read/write access to a group, then put individual users into that group to grant them permission.

Linux has a mechanism called `udev` that will automatically detect certain types of devices, and automatically apply permissions to those devices.  There are udev rules for USB and serial devices included with WeeWX.  These are installed as `/usr/lib/udev/rules.d/60-weewx.rules` for a DEB/RPM installation, or `/etc/udev/rules.d/60-weewx.rules` for a `pip` installation.  If you are using a USB or serial device that is not in the WeeWX core, you will probably have to add a udev rule for your device.

Changes to group membership require a logout/login, or a restart of the WeeWX daemon.

Changes to the udev rules are recognized immediately on modern systems, but on older systems you might have to unplug-then-replug the device.

#### Examples

For the WH23xx weather station, a USB-connected device with idVendor=10c4, idProduct=8486, put this rule in `/etc/udev/rules.d/60-weewx.rules` to give permission to the `weewx` user :
```
SUBSYSTEM=="usb",ATTRS{idVendor}=="10c4",ATTRS{idProduct}=="8468",MODE="0664",GROUP="weewx"
```

The SDR (software-defined radio) software, `rtl-sdr`, typically installs its udev rules with permissions granted to a dedicated group.  This might be `plugdev` or `plughw` - look at the rules in `/etc/udev/rules.d` to find out.  In this case, the rules are installed, but you must put the user who runs `weewxd` into the group specified in the rules.  

For example, this would grant permissions by putting the `weewx` user into the group `plugdev`:
```
sudo usermod -aG plugdev weewx
```

The serial port on many systems is configured so that anyone in the `dialout` group can read/write the serial ports.  So if you use a weather station connected to a serial port (e.g., a Davis station connected by serial or USB-to-serial), you must ensure that the user running WeeWX is in the group that can read/write the serial port.

For example, this would grant permissions by putting the `weewx` user into the group `dialout`:
```
sudo usermod -aG dialout weewx
```


### Binding to a network port

If you use the `interceptor` driver to listen on port 80, you might have problems when you run `weewxd` as a non-root user.  This is because only root is allowed to bind to ports lower than 1024.

For Linux systems, your options include:

- run `nginx` with a reverse proxy rule to send traffic to `weewxd`
- run `weewxd` as `root:root`
- listen on a higher port (this only works if you can change the port on whatever is sending data)
- use `authbind` to let `weewx:weewx` bind to port 80
- use `iptables` to redirect traffic from port 80 to a high port
- use `CAP_NET_BIND_SERVICE`

The reverse proxy is perhaps the most robust approach.  If you make interceptor listen on port 8080, the reverse proxy configuration looks something like this:
```
location / { 
  proxy_set_header X-Is-Reverse-Proxy "true";
  proxy_set_header Host $host;
  proxy_set_header X-Forwarded-Host $host;
  proxy_set_header X-Forwarded-Proto $scheme;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $remote_addr;
  proxy_pass http://localhost:8080;
}
```

here is the man page for authbind:

    https://manpages.ubuntu.com/manpages/noble/en/man1/authbind.1.html

stack overflow and superuser have details about CAP_NET_BIND_SERVICE:

    https://stackoverflow.com/questions/413807/is-there-a-way-for-non-root-processes-to-bind-to-privileged-ports-on-linux

    https://superuser.com/questions/710253/allow-non-root-process-to-bind-to-port-80-and-443

serverfault shows how to do the iptables redirect:

    https://serverfault.com/questions/112795/how-to-run-a-server-on-port-80-as-a-normal-user-on-linux


### Viewing the log

Most systems require administrative privileges to view the system log.
```
# systems that use syslog
sudo tail /var/log/syslog
sudo tail /var/log/messages

# systems that use systemd-journald
sudo journalctl -u weewx
```


### How to fix permissions

These instructions are for fixing the permissions and ownership of files in a DEB/RPM installation.  Files should be `664` and directories should be `775`.  Owner should be `weewx` and group should be `weewx`.

For the `WEEWX_ROOT` directory (configuration file, skins, and other extensions):
```
# set the permissions
sudo find /etc/weewx -type d -exec chmod 2775 {} \;
sudo find /etc/weewx -type t -exec chmod 664 {} \;
# set the owner and group
sudo chown -R weewx /etc/weewx
sudo chgrp -R weewx /etc/weewx
```

For the `HTML_ROOT` directory:
```
# set the permissions
sudo find /var/www/html/weewx -type d -exec chmod 2775 {} \;
sudo find /var/www/html/weewx -type t -exec chmod 664 {} \;
# set the owner and group
sudo chown -R weewx /var/www/html/weewx
sudo chgrp -R weewx /var/www/html/weewx
```

For the `SQLITE_ROOT` directory:
```
# set the permissions
sudo find /var/lib/weewx -type d -exec chmod 2775 {} \;
sudo find /var/lib/weewx -type t -exec chmod 664 {} \;
# set the owner and group
sudo chown -R weewx /var/lib/weewx
sudo chgrp -R weewx /var/lib/weewx
```
