## Working with permissions in a Unix environment

There are two general classes of users in a Unix environment: (1) privileged and (2) unprivileged.  A privileged user has the ability to do things to the system that affect how the system operates and could break the system if applied incorrectly.  For example, administrative privileges are typically required to upgrade the operating system or to install system software.  An unprivileged user can run software and save data, but only in ways that would not break the system. Usually you login to a computer as an unprivileged user, then you only *escalate* privilege when you do specific, administrative activities.  This helps prevent silly mistakes, and it provides a layer of protection against malicious behavior.

### sudo vs su

So how does an unprivileged user get permission to do system administration things?  There are two approaches: (1) temporary escalation using `sudo`, or (2) extended escalation by becoming `root`.  For temporary escalation, prefix a command with `sudo`.  For example, instead of `nano /etc/weewx/weewx.conf` you would do `sudo nano /etc/weewx/weewx.conf`.  For extended escalation, you login as the `root` user.  For example, you can become root by doing `su` or `su --login`.

Which approach is better?  It depends.  See the [Debian article about sudo](https://wiki.debian.org/sudo/) for pros and cons of each approach.

Beware that `sudo` may not be on your system, depending on how you installed/configured the operating system.  If there is no `sudo`, use your system's package manager to install it.

Note that BSD systems have the command `doas` as well as or instead of `sudo`, depending on how you installed/configured the operating system.

Privilege escalation usually affects you when you need to view or modify a file.  It will also affect you when you try to start and stop certain processes.

System files and directories are usually protected, so that only users with administrative privileges can modify them.  In some cases, administrative privileges are necessary even to *view* them.

Files and directories in your `HOME` directory do not have these restrictions.

Here are some situations where permissions matter when you are using WeeWX.

### Viewing the log

Most systems require administrative privileges to view the system log.
```
# systems that use syslog
sudo tail /var/log/syslog
sudo tail /var/log/messages
# systems that use systemd-journald
sudo journalctl -u weewx
```

### Modifying a configuration file or skin

If you installed WeeWX using `pip`, then all of the station settings and skins should be owned by you, so you can simply edit them:
```
nano ~/weewx-data/weewx.conf
```
If you installed WeeWX from a DEB/RPM package, then the station settings and skins are owned by the `weewx` user or by `root`.  If the files are owned by `weewx`, you must either be in the `weewx` group, or use `sudo` to modify them.
```
sudo nano /etc/weewx/weewx.conf
```

### Installing an extension

You must have read/write permissions on the WeeWX station "user" directory in order to install or remove extensions.  If you installed using `pip`, then you should be the owner of the `weewx-data` directory, so you can just invoke `weectl extension` and everything should work.  However, if you installed using a DEB or RPM package, then the `user` directory will be owned by `weewx` or `root`.

For example:
```
$ weectl extension install /var/tmp/gw1000-0.3.1.tar.gz
Request to install '/var/tmp/gw1000-0.3.1.tar.gz'
Extracting from tar archive /var/tmp/gw1000-0.3.1.tar.gz
Traceback (most recent call last):
   [...]
PermissionError: [Errno 13] Permission denied: '/etc/weewx/bin/user/gw1000.py'
```

```
$ sudo weectl extension install /var/tmp/gw1000-0.3.1.tar.gz
Request to install '/var/tmp/gw1000-0.3.1.tar.gz'
Extracting from tar archive /var/tmp/gw1000-0.3.1.tar.gz
Saving installer file to /etc/weewx/bin/user/installer/GW1000
Saved configuration dictionary. Backup copy at /etc/weewx/weewx.conf.20210426101025
Finished installing extension '/var/tmp/gw1000-0.3.1.tar.gz'
```

### Reading/writing to a database

In a default configuration, the WeeWX database is world-readable, but writable only by the owner.  So you should be able to read the WeeWX database no matter how you installed WeeWX.
```
# pip install
sqlite3 ~/weewx-data/archive/weewx.sdb
# deb/rpm install
sqlite3 /var/lib/weewx.sdb
```

### Reading/writing data to a device

By default, only a privileged user can write to USB or serial devices.

Independent of permissions, most USB and serial devices are accessible to only one process at a time.  For example, if `weewxd` is running and communicating with the device `/dev/ttyUSB0`, then you will not be able to read/write to the device `/dev/ttyUSB0` even if you have sufficient permissions.


## Add a user to a group

If you add yourself to the `weewx` group, then you can modify configurations and skins without having to `sudo`.
```
sudo usermod -a -g weewx $USER
```
