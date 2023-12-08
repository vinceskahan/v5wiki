## Working with permissions in a Unix environment


### Viewing the log

Most systems require administrative privileges to view the system log.
```
sudo tail /var/log/syslog
```

### Modifying a configuration file or skin
```
sudo nano /home/weewx/weewx.conf
```

### Installing an extension

### Reading/writing to a database

### Reading/writing data to a device

## Add a user to a group
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
