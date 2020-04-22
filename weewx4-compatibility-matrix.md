# Compatibility matrix for weewx

This page is an attempt to make sense of the dependencies and upgrade paths for weewx.  Although weewx (still) has few dependencies itself, the python2/python3 change introduced significant complexity, and the libraries weewx uses (notably python imaging and cheetah) vary widely between platforms.

## Dependencies

This is an enumeration of the weewx dependencies for each major operating system release.

<table>
<tr><td>OS</td><td>python2</td><td>python3</td></tr>
<tr valign="top">
<td>debian10<br>31mar2020</td>
<td>
<pre>
# weewx_x.y.z-n_all.deb
#
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
#
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

<tr valign="top">
<td>debian9<br>31mar2020</td>
<td>
<pre>
# weewx_x.y.z-n_all.deb
#
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
# no deb package
#
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
python3 setup.py install
</pre>
</td>
</tr>

<tr valign="top">
<td>debian8</td>
<td>
<pre>
# weewx_x.y.z-n_all.deb
#
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
<pre>
# no deb package
#
# required
# optional
# weewx
python3 setup.py install
</pre>
</td>
</tr>

<tr valign="top">
<td>debian7</td>
<td>
<pre>
# weewx_x.y.z-n_all.deb
#
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
<pre>
# no deb package
#
# required
# optional
# weewx
python3 setup.py install
</pre>
</td>
</tr>

<tr valign="top">
<td>centos8<br/>09apr2020</td>
<td>
<pre>
# no rpm package
#
# required
pip2 install configobj
pip2 install pil
pip2 install cheetah
pip2 install pyusb
pip2 install pyserial
# optional
pip2 install pyephem
pip2 install mysqlclient
# weewx
python2 setup.py install
</pre>
</td>
<td>
<pre>
# weewx-x.y.z-n.el8.noarch.rpm
#
# required
dnf install python3-configobj
dnf install python3-pillow
pip3 install cheetah3
dnf install python3-pyusb
dnf install python3-pyserial
# optional
pip3 install pyephem
dnf install python3-pymysql
# weewx
dnf install weewx
</pre>
</td>
</tr>

<tr valign="top">
<td>centos7<br>31mar2020</td>
<td>
<pre>
# weewx-x.y.z-n.el7.noarch.rpm
#
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
<pre>
# no rpm package
#
# required
pip3 install configobj
pip3 install pil
pip3 install cheetah3
pip3 install pyusb
pip3 install pyserial
# optional
pip3 install ephem
yum install mysql-client
pip3 install mysqldb
# weewx
python3 setup.py install
</pre>
</td>
</tr>

</table>



## Upgrade paths

This is an enumeration of the upgrade paths for weewx, for various operating system packages.

| from | to | command |
|---|---|---|
| any debian<br>python2 | debian10 or later<br>python3 | apt-get install weewx<br>apt-get install python3-weewx |
| any debian<br>python2 | any debian<br>python2 | apt-get install weewx |
| centos7<br>python2 | centos8<br>python3 | yum update weewx |

## Issues

### debian packaging
* As of weewx 4, the python2 weewx is called `weewx` and the python3 package is called `python3-weewx`.  The python3 package is supported only for debian10 and later, since the python3 packages on which weewx depends are not available in a standard debian9, debian8, or debian7 distribution.  (It might be possible to hack that using backports, but that is left as an exercise to the reader)
* The packages `weewx` and `python3-weewx` cannot co-exist, since they both live in `/usr/share/weewx`.  The package `python3-weewx` replaces `weewx`.  
* We could use `/usr/share/weewx` and `/usr/share/weewx-python3` to distinguish between `weewx` and `python3-weewx`.  But then what about data `/var/lib/weewx`?  And would this not just confuse users even more?
* If `weewx` and `python3-weewx` cannot co-exist, then they can at least be forward-compatible.  That means installing `python3-weewx` should not destroy data/config of a `weewx` install, just the code.  In that sense it looks like a version upgrade.

### redhat packaging
* As of weewx 4, the python2 package is `el7` and the python3 package is `el8`.  This reflects the python support in the 7 and 8 releases of the operating system.

### suse packaging

### bsd packaging

## Testing

| platform | install/upgrade | method | date | result |
|---|---|---|---|---|
| debian10 | weewx-4.0.0b18 | `dpkg -i` | 22apr2020 | OK |
| debian10 | weewx-3.9.2 to weewx-4.0.0b18 | `dpkg -i` | 22apr2020 | OK |
| debian10 | weewx-3.9.2 | `dpkg -i` | 22apr2020 | OK |
| debian9 | weewx-4.0.0b18 | `dpkg -i` | 22apr2020 | OK |
| debian9 | weewx-3.9.2 to weewx-4.0.0b18 | `dpkg -i` | 22apr2020 | OK |
| debian9 | weewx-3.9.2 | `dpkg -i` | 22apr2020 | OK |
| debian8 | weewx-4.0.0b18 | `dpkg -i` | 22apr2020 | FAIL: two instances running; stop does not stop |
| debian8 | weewx-3.9.2 to weewx-4.0.0b18 | `dpkg -i` | 22apr2020 | FAIL: stop does not stop |
| debian8 | weewx-3.9.2 | `dpkg -i` | 22apr2020 | OK |
