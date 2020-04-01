# Compatibility matrix for weewx

This page is an attempt to make sense of the dependencies and upgrade paths for weewx.  Although weewx (still) has few dependencies itself, the python2/python3 change introduced significant complexity, and the libraries weewx uses (notably python imaging and cheetah) vary widely between platforms.

## Dependencies

This is an enumeration of the weewx dependencies for each major operating system release.

<table>
<tr><td>OS</td><td>python2</td><td>python3</td></tr>
<tr>
<td>debian10<br>31mar2020</td>
<td>
<pre>
# weewx_x.y.z-n_all.deb
# required
apt-get install python-configobj
apt-get install python-pil
apt-get install python-cheetah
apt-get install python-usb
apt-get install python-serial
# optional
apt-get install python-ephem
apt-get install mariadb-client
apt-get install python-mysqldb
# weewx
apt-get install weewx
</pre>
</td>
<td>
<pre>
# python3-weewx_x.y.z-n_all.deb
# required
apt-get install python3-configobj
apt-get install python3-pil
apt-get install python3-cheetah
apt-get install python3-usb
apt-get install python3-serial
# optional
apt-get install python3-ephem
apt-get install mariadb-client
apt-get install python3-mysqldb
# weewx
apt-get install python3-weewx
</pre>
</td>
</tr>

<tr>
<td>debian9<br>31mar2020</td>
<td>
<pre>
# weewx_x.y.z-n_all.deb
# required
apt-get install python-configobj
apt-get install python-pil OR python-imaging
apt-get install python-cheetah
apt-get install python-usb
apt-get install python-serial
# optional
apt-get install python-ephem
apt-get install mysql-client
apt-get install python-mysqldb
# weewx
apt-get install weewx
</pre>
</td>
<td>
<pre>
# python3-weewx_x.y.z-n_all.deb
# required
apt-get install python3-configobj
apt-get install python3-pil
apt-get install python3-usb
apt-get install python3-serial
pip3 install cheetah3
# optional
apt-get install python3-ephem
apt-get install mysql-client
apt-get install python3-mysqldb
# weewx
apt-get install python3-weewx
</pre>
</td>
</tr>

<tr>
<td>debian8</td>
<td>
<pre>
# weewx_x.y.z-n_all.deb
# required
apt-get install python-configobj
apt-get install python-pil OR python-imaging
apt-get install python-cheetah
apt-get install python-usb
apt-get install python-serial
# optional
apt-get install python-dev
apt-get install python-pip
pip install pyephem
apt-get install mysql-client
apt-get install python-mysqldb
# weewx
apt-get install weewx
</pre>
</td>
<td>
# install weewx using setup.py
</td>
</tr>

<tr>
<td>debian7</td>
<td>
<pre>
# weewx_x.y.z-n_all.deb
# required
apt-get install python-configobj
apt-get install python-pil OR python-imaging
apt-get install python-cheetah
apt-get install python-usb
apt-get install python-serial
# optional
apt-get install python-dev
apt-get install python-pip
pip install pyephem
apt-get install mysql-client
apt-get install python-mysqldb
# weewx
apt-get install weewx
</pre>
</td>
<td>
# install weewx using setup.py
</td>
</tr>

<tr>
<td>centos8</td>
<td>
# install weewx using setup.py
</td>
<td>
<pre>
# weewx-x.y.z-n.el8.noarch.rpm
# required
yum install python-configobj
yum install python-pillow
yum install python-cheetah
yum install pyusb
yum install pyserial
# optional
yum install python-setuptools
easy_install install pyephem
yum install mysql-client
yum install python-mysqldb
# weewx
yum install weewx
</pre>
</td>
</tr>

<tr>
<td>centos7<br>31mar2020</td>
<td>
<pre>
# weewx-x.y.z-n.el7.noarch.rpm
# required
yum install python-configobj
yum install python-pillow
yum install python-cheetah
yum install pyusb
yum install pyserial
# optional
yum install python-setuptools
easy_install install pyephem
yum install mysql-client
yum install python-mysqldb
# weewx
yum install weewx
</pre>
</td>
<td>
# install weewx using setup.py
</td>
</tr>

</table>



## Upgrade paths

This is an enumeration of the upgrade paths for weewx, for various operating system packages.

```
apt-get install weewx
apt-get install python3-weewx
```

## Issues

* The packages `weewx` and `python3-weewx` cannot co-exist, since they both live in `/usr/share/weewx`.  The package `python3-weewx` replaces `weewx`.  
  * We could use `/usr/share/weewx` and `/usr/share/weewx-python3` to distinguish.  But then what about data `/var/lib/weewx`?  And would this not just confuse users even more?
  * If they cannot co-exist, then they can at least be forward-compatible.  That means installing `python3-weewx` should not destroy data/config of a `weewx` install, just the code.  In that sense it looks like a version upgrade.

