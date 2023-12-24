## Working with permissions in a Unix environment

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
If you installed WeeWX from a DEB/RPM package, then the station settings and skins are owned by the `weewx` user.  You must either be in the `weewx` group, or use `sudo` to modify them.
```
sudo nano /etc/weewx/weewx.conf
```

### Installing an extension

### Reading/writing to a database

In a default configuration, the WeeWX database is world-readable, but writable only by the owner.  So you should be able to read the WeeWX database no matter how you installed WeeWX.
```
# pip install
sqlite3 ~/weewx-data/archive/weewx.sdb
# deb/rpm install
sqlite3 /var/lib/weewx.sdb
```

### Reading/writing data to a device

## Add a user to a group

If you add yourself to the `weewx` group, then you can modify configurations and skins without having to `sudo`.
```
sudo usermod -a -g weewx $USER
```

## Example
```
$ /home/weewx/bin/wee_extension --install /var/tmp/gw1000-0.3.1.tar.gz
Request to install '/var/tmp/gw1000-0.3.1.tar.gz'
Extracting from tar archive /var/tmp/gw1000-0.3.1.tar.gz
Traceback (most recent call last):
   [...]
PermissionError: [Errno 13] Permission denied: '/home/weewx/bin/user/gw1000.py'
```

```
$ sudo /home/weewx/bin/wee_extension --install /var/tmp/gw1000-0.3.1.tar.gz
Request to install '/var/tmp/gw1000-0.3.1.tar.gz'
Extracting from tar archive /var/tmp/gw1000-0.3.1.tar.gz
Saving installer file to /home/weewx/bin/user/installer/GW1000
Saved configuration dictionary. Backup copy at /home/weewx/weewx.conf.20210426101025
Finished installing extension '/var/tmp/gw1000-0.3.1.tar.gz'
```
