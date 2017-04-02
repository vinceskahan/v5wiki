This is how to convert from a setup.py installation to a deb package installation.

1. Be sure weewx is not running
```
sudo /etc/init.d/weewx stop
```

2. Move the startup script out of the way
```
sudo mv /etc/init.d/weewx /etc/init.d/weewx-old
```

3. Copy any existing data to the location where the deb package will find it
```
sudo mkdir /var/lib/weewx
sudo cp /home/weewx/archive/*.sdb /var/lib/weewx
```

4. Do the installation
```
sudo dpkg -i weewx_x.y.z-r_all.deb
```

5. Copy any customizations


if you have made any modifications, copy them over then restart weewx.

eg:-

 /home/weewx/weex.conf -> /etc/weewx/weewx.conf

 /home/weewx/skins -> /etc/weewx/skins

 /home/weewx/bin/user -> /usr/share/weewx/user

