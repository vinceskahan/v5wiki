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

#### Desired behavior

* As of weewx 4, the python2 weewx should be called `python-weewx` and the python3 package should be called `python3-weewx`.  The python3 package is supported only for debian10 and later, since the python3 packages on which weewx depends are not available in a standard debian9, debian8, or debian7 distribution.  (It might be possible to hack that using backports, but that is left as an exercise to the reader)
* The packages `python-weewx` and `python3-weewx` cannot co-exist, since they both live in `/usr/share/weewx`.  They differ only by (1) the contents of `/etc/default/weewx` and (2) the `Depends` in the `debian/control` file.
* The package `python-weewx` replaces `weewx`.
* The package `python3-weewx` replaces `weewx`.  
* The `weewx` package has been marked as `squeeze` in the apt repository.
* The `python-weewx` package should be marked as `squeeze` in the apt repository.
* The `python3-weewx` package should be marked as `buster` in the apt repository.
* If a user specifies or stays on the `squeeze` release, then that user should continue to use python2.
* If a user specifies or changes to the `buster` release, then that user should get python3 (only for debian10 or later).

#### option 1: weewx is virtual package

`python-weewx` and `python3-weewx` are actual packages.  the only differences between them are:

* the value of `PYTHON` in `/etc/default/weewx`
* the value of `Depends` in the control file

Would it be possible to use `update-alternatives` to switch between python2 and python3?

* how to build a virtual package?
* how to build weewx, python-weewx, and python3-weewx from a single control file?

#### option 2: single package with smart Depends

Create a single package called `weewx`.  In `debian/control`, make the `Depends` smart - it should default to python3 and associated packages, but if any of those fail or are not available, it falls back to the python and associated packages.

Is this even possible?  A quick look at the syntax for `Depends` indicates `no`.  Although you can do a conditional dependency for a single package (e.g., `python-pillow | python-pil`), you cannot do combined boolean logic.  We would need something like `(python3 && python3-pil && python3-configobj && python3-cheetah) || (python && (python-pillow | python-pil) && python-configobj && python-cheetah)`

#### option 3: two apt repositories

There would be two packages: `python-weewx` and `python3-weewx`.  Each package would have the alias `weewx`, so you could install using `apt-get install weewx` for either one.

There would be two apt repositories.  The `squeeze` repository would have the `python-weewx` package.  The `buster` repository would have the `python3-weewx` package.

Users who have already been using weewx3 with python2 would continue as before.

Those who want python2 would use this:
```
deb [arch=all] http://weewx.com/apt/ squeeze main
```
Those who want python3 would use this (only on debian10 or later):
```
deb [arch=all] http://weewx.com/apt/ buster main
```
Can this be done with a single `/etc/apt/sources.list.d/weewx.list`?  If so, then how to distinguish `squeeze` versus `buster` at install time?

Otherwise user must choose one pre-install:
```
# for python2
wget -qO - http://weewx.com/apt/weewx.list | sudo tee /etc/apt/sources.list.d/weewx.list
# for python3
wget -qO - http://weewx.com/apt/python3-weewx.list | sudo tee /etc/apt/sources.list.d/python3-weewx.list
```
What happens if both `weewx.list` and `python3-weewx.list` are in `sources.list.d`?  Does that cause problems for the `weewx` package?

### redhat packaging

* As of weewx 4, the python2 package is `el7` and the python3 package is `el8`.  This reflects the python support in the 7 and 8 releases of the operating system.

### suse packaging

### bsd packaging

## Testing

| platform | install/upgrade | method | date | result |
|---|---|---|---|---|
| debian10 | weewx-4.0.0b18 | `dpkg -i` | 2020.04.22 | OK |
| debian10 | weewx-3.9.2 to weewx-4.0.0b18 | `dpkg -i` | 2020.04.22 | OK |
| debian10 | weewx-3.9.2 | `dpkg -i` | 2020.04.22 | OK |
| debian9 | weewx-4.0.0b18 | `dpkg -i` | 2020.04.22 | OK |
| debian9 | weewx-3.9.2 to weewx-4.0.0b18 | `dpkg -i` | 2020.04.22 | OK |
| debian9 | weewx-3.9.2 | `dpkg -i` | 2020.04.22 | OK |
| debian8 | weewx-4.0.0b18 | `dpkg -i` | 2020.04.22 | OK |
| debian8 | weewx-3.9.2 to weewx-4.0.0b18 | `dpkg -i` | 2020.04.22 | OK |
| debian8 | weewx-3.9.2 | `dpkg -i` | 2020.04.22 | OK |
