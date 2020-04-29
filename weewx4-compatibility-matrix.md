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
# required for cheetah
dnf install epel-release
# required
dnf install python3-configobj
dnf install python3-pillow
dnf install python3-cheetah
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

* As of weewx 4, the python2 weewx should be called `python-weewx` and the python3 package should be called `python3-weewx`.
* The `python3-weewx` package will be supported only for debian10 and later, since the python3 packages on which weewx depends are not available in a standard debian9, debian8, or debian7 distribution.  (It might be possible to hack that using backports, but that is left as an exercise to the reader)
* The packages `python-weewx` and `python3-weewx` cannot co-exist, since they both live in `/usr/share/weewx`.  They differ only by
   * the contents of `/etc/default/weewx`
   * the `Depends` in the `debian/control` file
* The package `python-weewx` replaces `weewx`.
* The package `python3-weewx` replaces `weewx`.  
* The `weewx` package has been marked as `squeeze` in the apt repository.
* The `python-weewx` package should be marked as `squeeze` in the apt repository.
* The `python3-weewx` package should be marked as `buster` in the apt repository.
* If a user specifies or stays on the `squeeze` release, then that user should continue to use python2.
* If a user specifies or changes to the `buster` release, then that user should get python3.

#### Problems

* When I put all of the packages into a single control file, I get three packages: `weewx`, `python-weewx`, and `python3-weewx`.  Only the `weewx` package gets all of the actual files, and the dependencies are not right.
* When I do the transitional package configuration, with `weewx` and `python-weewx` in one control and `weewx` and `python3-weewx` in another control, I get two .deb files for each control.
* When using dh-python, it runs, but it munges the shebang.  So for now, sticking with home-grown `install` target and binary overrides, with two separate invocations of debbuild - one for python-weewx and one for python3-weewx
* Must ensure that any `wee_*` invocations in the postinst use `/usr/bin/wee_*` not `/usr/share/wee_*`.
* Since the `postinst` invokes `wee_config`, we might have to make `python-config` part of the `Pre-Depends`.  But debian docs say pretty strongly to not use `Pre-Depends` unless absolutely necessary.

#### current approach (apr2020): two packages named `weewx`

* create package `weewx` with filename `python-weewx` for python2, associated with distribution `squeeze`
* create package `weewx` with filename `python3-weewx` for python3, associated with distribution `buster`
* two repositories now, one for `squeeze` and one for `buster` (still using aptly to manage the repos)

#### option 1: virtual package

`python-weewx` and `python3-weewx` are actual packages, and `weewx` is a virtual package.  To do this, `python-weewx` and `python3-weewx` each have a `Provides` in the control.  Unfortunately this does not fix the problem of using `weewx` to install - you cannot do `apt-get install weewx`, but must do `apt-get install python-weewx`.

Would it be possible to use `update-alternatives` to switch between python2 and python3?

#### option 2: transition package

> Basically, the solution is to define a binary dummy package with the same name as the old package in the control file of the new package. The new source package takes over the binary dummy package, and the old source package, which is then binaryless, will be cleaned up by rene, an archive cleanup tool. 
https://wiki.debian.org/RenamingPackages

This gets the `apt-get install weewx` to work, since we make `weewx` have a `Depends` on `python-weewx` and `python3-weewx`.  Requires two separate control files.

However, we do not want the transition package to be cleaned up.

#### option 3: single package with smart Depends

Create a single package called `weewx`.  In `debian/control`, make the `Depends` smart - it should default to python3 and associated packages, but if any of those fail or are not available, it falls back to the python and associated packages.

Is this even possible?  A quick look at the syntax for `Depends` indicates `no`.  Although you can do a conditional dependency for a single package (e.g., `python-pillow | python-pil`), you cannot do combined boolean logic.  We would need something like `(python3 && python3-pil && python3-configobj && python3-cheetah) || (python && (python-pillow | python-pil) && python-configobj && python-cheetah)`

#### Distribution: two apt repositories

There would be two apt repositories.  The `squeeze` repository would have the `python-weewx` package.  The `buster` repository would have the `python3-weewx` package.

Users who upgrade from weewx3 to weewx4, but continue with python2, do not have to change their apt configuration.

Users who do a new install with python2 would use this:
```
deb [arch=all] http://weewx.com/apt/ squeeze main
```
Users who do a new install with python3 would use this (only on debian10 or later):
```
deb [arch=all] http://weewx.com/apt/ buster main
```

### redhat packaging

* As of weewx 4, the python2 package is `el7` and the python3 package is `el8`.  This reflects the python support in the 7 and 8 releases of the operating system.

### suse packaging

### bsd packaging

## Testing

| platform | install/upgrade | method | date | result |
|---|---|---|---|---|
|debian| | | | |
| debian10 | python3-weewx-4.0.0b18-3 | `apt-get install weewx` | 2020.04.28 | OK |
| debian10 | python-weewx-4.0.0b18-3 | `apt-get install weewx` | 2020.04.28 | OK |
| debian10 | weewx-3.9.2-1 to python-weewx-4.0.0b18-4 | `apt-get install weewx` | 2020.04.29 | OK |
| debian9 | weewx-4.0.0b18-3 | `apt-get install weewx` | 2020.04.28 | OK |
|debian<br>dpkg| | | | |
| debian10 | weewx-4.0.0b18-1 | `dpkg -i` | 2020.04.22 | OK |
| debian10 | weewx-3.9.2-1 to weewx-4.0.0b18-1 | `dpkg -i` | 2020.04.22 | OK |
| debian10 | weewx-3.9.2-1 | `dpkg -i` | 2020.04.22 | OK |
| debian9 | weewx-4.0.0b18-1 | `dpkg -i` | 2020.04.22 | OK |
| debian9 | weewx-3.9.2-1 to weewx-4.0.0b18-1 | `dpkg -i` | 2020.04.22 | OK |
| debian9 | weewx-3.9.2-1 | `dpkg -i` | 2020.04.22 | OK |
| debian8 | weewx-4.0.0b18-1 | `dpkg -i` | 2020.04.22 | OK |
| debian8 | weewx-3.9.2-1 to weewx-4.0.0b18-1 | `dpkg -i` | 2020.04.22 | OK |
| debian8 | weewx-3.9.2-1 | `dpkg -i` | 2020.04.22 | OK |
|ubuntu| | | | |
| ubuntu 20.04 | python3-weewx-4.0.0b18-3 | `apt-get install weewx` | 2020.04.28 | OK |
|centos| | | | |
| centos8 | weewx-4.0.0b18-2 | `yum install weewx` | 2020.04.27 | OK |
| centos7 | weewx-4.0.0b18-2 to weewx-3.9.2-1 | `yum downgrade weewx` | 2020.04.27 | OK |
| centos7 | weewx-4.0.0b18-2 | `yum install weewx` | 2020.04.27 | OK |
| centos7 | weewx-3.9.2-1 to weewx-4.0.0b18-2 | `yum update weewx` | 2020.04.27 | OK |
|suse| | | | |
|openbsd| | | | |

## References

```
# how to put the same package into different apt repos as different distributions
https://groups.google.com/forum/#!topic/aptly-discuss/YTLXLUqBbFo
```
