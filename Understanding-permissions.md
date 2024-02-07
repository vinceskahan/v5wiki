## Working with permissions in a Unix environment

This is an introduction to how permissions work in a Unix environment, with functional examples that you might encounter with WeeWX.

* [Privilege escalation: sudo vs su](#sudo-vs-su)
* [Viewing the log](#viewing-the-log)
* [Modifying configuration/skin](#modifying-a-configuration-file-or-skin)
* [Installing extensions](#installing-an-extension)
* [Modifying the database](#readingwriting-to-a-database)
* [Read/Write to a device](#readingwriting-data-to-a-device)
* [Binding to a network port](#binding-to-a-network-port)

For each file and directory, there is a set of permissions that define who can read and write that file or directory.  The permissions are defined by `owner`, `group`, and `world`.  This lets you say "only bill can write to file X, but anyone can read it", or "anyone in the `weewx` group can write to this directory, and no one else can even read it".  Since USB and serial devices are also just files (a special kind of file, but still just files), the same permissions system applies to them.

There are two general classes of users in a Unix environment: (1) privileged and (2) unprivileged.  A privileged user has the ability to do things to the system that affect how the system operates and could break the system if applied incorrectly.  For example, administrative privileges are typically required to upgrade the operating system or to install system software.  An unprivileged user can run software and save data, but only in ways that would not break the system. Usually you login to a computer as an unprivileged user, then you only *escalate* privilege when you do specific, administrative activities.  This helps prevent silly mistakes, and it provides a layer of protection against malicious behavior.

Before V5, WeeWX ran as `root`.  When run this way, permissions are not an issue - `root` has permission to do anything.  With V5, WeeWX runs as a non-root user, either the dedicated `weewx` user (DEB/RPM installs), or the user who installed WeeWX (pip installs).  Running as a non-root user is considered best practice - it minimizes the damage to the system should something go awry, and it is more secure against nefarious attacks.


### The `weewx` user and group

For DEB/RPM installations, the WeeWX files are owned by the `weewx` user.  The files are also in a group called `weewx`, so anyone in the `weewx` group will also have permission to modify the files.

When you are in the `weewx` group, you do not have to `sudo` to modify the WeeWX files, but you do need `sudo` to start/stop the `weewxd` daemon.

If you installed WeeWX using the DEB/RPM package, the installer will put you into the `weewx` group.

If somehow that did not happen, you can add yourself to the `weewx` group:
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
sqlite3 /var/lib/weewx/weewx.sdb
```

### Reading/writing data to a device

By default, only a privileged user can write to USB or serial devices.  If you want someone other than root to read/write a USB or serial device, then you must change the permissions on that device.  Usually it is best to define read/write access to a group, then put individual users into that group to grant them permission.  Linux has a mechanism called `udev` that will automatically detect certain types of devices, and automatically apply permissions to those devices.

Independent of permissions, most USB and serial devices are accessible to only one process at a time.  For example, if `weewxd` is running and communicating with the device `/dev/ttyUSB0`, then you will not be able to read/write to the device `/dev/ttyUSB0` even if you have sufficient permissions.


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
