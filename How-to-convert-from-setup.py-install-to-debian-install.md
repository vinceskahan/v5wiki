This is how to convert from a setup.py installation to a deb package installation.

<ol>
<li>be sure weewx is not running</li>
```sudo /etc/init.d/weewx stop```

<li>move the startup script out of the way</li>
```sudo mv /etc/init.d/weewx /etc/init.d/weewx-old```

<li>copy any existing data to the location where the deb package will find it</li>
```
sudo mkdir /var/lib/weewx
sudo cp /home/weewx/archive/*.sdb /var/lib/weewx
```

<li>do the installation</li>
```
sudo dpkg -i weewx_x.y.z-r_all.deb
```

<li>copy any customizations</li>

if you have made any modifications, copy them over then restart weewx.
```
/home/weewx/weex.conf -> /etc/weewx/weewx.conf
/home/weewx/skins -> /etc/weewx/skins
/home/weewx/bin/user -> /usr/share/weewx/user
</ul>
