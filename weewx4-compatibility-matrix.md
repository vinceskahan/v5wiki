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
</td>
</tr>

<tr>
<td>debian7</td>
<td>
<pre>
apt-get install weewx
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
</td>
</tr>

<tr>
<td>centos8</td>
<td></td>
<td></td>
</tr>

<tr>
<td>centos7</td>
<td>
<pre>
# required
yum install python-configobj
apt-get install python-pil OR python-imaging
yum install python-cheetah
pip install pyusb
pip install pyserial
# optional
apt-get install python-dev
apt-get install python-pip
pip install pyephem
apt-get install mysql-client
apt-get install python-mysqldb
</pre>
</td>
<td></td>
</tr>

</table>



## Upgrade paths

This is an enumeration of the upgrade paths for weewx, for various operating system packages.

