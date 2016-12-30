##Running weewx on a Raspberry Pi##

Running weewx on a Raspberry Pi has become very popular. You'll have to look elsewhere for general instructions on setting up your RPi, but there are a few problem specific to weewx.

###Time###

First, you simply must run NTP on the RPi. It is not possible to create a dependable data logging system without an accurate source of time.

Second, the RPi does not have an onboard battery-powered clock. Instead, by default, the software is configured with a "fake clock" (Debian package `fake-hwclock`). The "fake clock" regularly records the time on the hard disk. This time is then used to set the clock during the reboot after a power failure. Unfortunately, this time could be hours behind the real time, depending on how long the power was out. 

Meanwhile, if you are running weewx as a daemon, weewx will also start up and try to run with this seriously out-of-date time. When it then stores something in the database, it will either be stored under a too-old timestamp, or there may already be a record for that timestamp (duplicate primary key), left over from before the power outage.

Eventually NTP starts up, finds an authoritative time source, then sets the time correctly, but this could take several minutes and, by then, the damage is done.

There are a number of solutions. Here are two.

#####A. Add a hardware clock#####

The best solution is to add a real-time clock. They are inexpensive ($5-$15 USD) and solve the problem permanently. Users have had good luck with these clocks:

* [Jameco Electronics](http://www.jameco.com/webapp/wcs/stores/servlet/ProductDisplay?langId=-1&storeId=10001&catalogId=10001&productId=2217625&CID=AVANT)
* [SunFounder DS3231 on Amazon](http://www.amazon.com/SunFounder-DS3231-Precision-Raspberry-Arduino/dp/B00HF4NUSS)

For Raspberry Pi-2 models, detailed RTC installation and Raspbian configuration instructions can be found from [W0CHP's WX Station web site](http://wx.w0chp.net/setup/RPi2-B_RTC.html).

For Raspberry models running Jessie, see some hints on the page [pi RTC with jessie](https://github.com/weewx/weewx/wiki/pi-RTC-with-raspbian-jessie).

#####B. Remove the fake clock#####

As of Version 3.4, Weewx will not start up until the system time is later than 1 January 2000. So, a simple solution is to remove the fake clock. Without the fake clock, the system time will literally start at "time zero," or unix epoch time zero, which is midnight 1-Jan-1970, preventing weewx from starting up until NTP sets the true time.

To remove the fake clock:

~~~~~
$ sudo apt-get purge fake-hwclock
~~~~~

###Use a high-quality SD card###

The SD card supplied with most RPis seems to be of very low quality. Make sure you use a good, Class 10 card. User William Phelps reports, "You can usually spot a failing card by watching the kernel I/O wait time. Once the card starts to go, the I/O wait time will increase significantly."

Many users have had good luck with the better Sandisk SD cards. In increasing order of quality (and expense):

* Sandisk Extreme
* Sandisk Extreme Plus (what I use)
* Sandisk Extreme Pro


###FTP###

Many Raspbian versions of Debian do not seem to include an FTP client. You may have to install this if you plan on using FTP.

~~~~~
$ sudo apt-get install ftp
~~~~~


###Use a good power supply###

A lot of problems with the RPi seem to center around inadequate power supplies.  Many weather stations can demand a lot of current through their USB connection. Make sure your RPi can supply it, either by getting a good power supply, or, alternatively, by using a powered USB hub.  I use an [Innogear 1.5 A supply](http://www.amazon.com/gp/product/B00J3IB7A2/), which costs well under $10 USD from Amazon, but, unfortunately, it is not offered any more. This [Samsung charger](https://www.amazon.com/Samsung-Adapter-5-Feet-Charging-Cables/dp/B00CQS0S7E/ref=sr_1_1) is essentially similar.
        
###Run a lightweight web server###

The RPi is astonishingly powerful for its size, but it does have its limitations. If you plan on running a webserver on it, perhaps to serve your home, then use a lightweight one, such as [lighttpd](http://www.lighttpd.net/), or [nginx](http://nginx.org/). Apache works, but it uses far more memory. In my tests:

<table style="width:50%">
<caption>Memory requirements of 3 common webservers on a Raspberry Pi</caption>
<tr>
<td><b>Webserver<b></td><td><b>VIRT</b></td><td><b>RES</b></td>
</tr>
<tr>
<td>Apache</td><td>222 Mb</td><td>3 Mb</td>
</tr>
<tr>
<td>lighttpd</td><td>7 Mb</td><td>2 Mb</td>
</tr>
<tr>
<td>nginx</td><td>11 Mb</td><td>1.7 Mb</td>
</tr>
</table>