Users who wish to switch from the old SysVinit init.d startup files to the newer systemd service files have some cleanup to do.  This page provides some pointers for how to do so. When in doubt, consult your operating system documentation, support forums, and the usual web search engines.


### RedHat-based systems

```
# tested on centos 7
sudo chkconfig --del weewx
sudo rm /etc/init.d/weewx
```

### Debian-based systems

```
# tested on debian11
sudo update-rc.d weewx remove
sudo rm /etc/init.d/weewx
```
