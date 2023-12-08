## Running WeeWX on a Raspberry Pi

Running WeeWX on a Raspberry Pi has become very popular. You'll have to look elsewhere for general instructions on setting up your RPi, but here are a few problems that are specific to WeeWX.

### Time

First, you simply must run a time synchronization service. It is not possible to create a dependable data logging system without an accurate source of time. See the Wiki article [*Time services*](Time-services).

Second, the RPi does not have an onboard battery-powered clock. Instead, by default, the software is configured with a "fake clock," which regularly records the time on the hard disk. If there is a power failure, then this time is used to set the clock during the reboot. Unfortunately, this time could be hours behind the real time, depending on how long the power was out. 

Meanwhile, if you are running WeeWX as a daemon, WeeWX will also start up and try to run with this seriously out-of-date time. When it comes time to store a record to the database, it will either be stored under a too-old timestamp, or there may already be a record for that timestamp (duplicate primary key), left over from before the power outage.

Eventually, whatever time synchronization service you are using starts up, finds an authoritative time source, then sets the time correctly, but this could take several minutes and, by then, the damage has been done.

There are a number of solutions. Here are two.

#### A. Add a hardware clock

The best solution is to add a real-time clock. They are inexpensive ($5-$15 USD) and solve the problem permanently. Users have had good luck with these clocks:

* [Jameco Electronics](http://www.jameco.com/webapp/wcs/stores/servlet/ProductDisplay?langId=-1&storeId=10001&catalogId=10001&productId=2217625&CID=AVANT)
* [SunFounder DS3231](http://www.amazon.com/SunFounder-DS3231-Precision-Raspberry-Arduino/dp/B00HF4NUSS) on Amazon.
* [Makerfire DS1307](https://www.amazon.com/dp/B00ZOXWHK4/ref=cm_sw_r_cp_awdb_t1_I4CbBbHK8NDYP) on Amazon
* [DS3231 module](https://m.aliexpress.com/item/32315883368.html) on Aliexpress. Under $1!

For Raspberry Pi-2 models, detailed RTC installation and Raspbian configuration instructions can be found from [W0CHP's WX Station web site](http://wx.w0chp.net/setup/RPi2-B_RTC.html).

For Raspberry models running Jessie, see some hints on the page [pi RTC with jessie](https://github.com/weewx/weewx/wiki/pi-RTC-with-raspbian-jessie).

#### B. Remove the fake clock

As of Version 3.4, WeeWX will not start up until the system time is later than 1 January 2000. So, a simple solution is to remove the fake clock. Without the fake clock, the system time will literally start at "time zero," or unix epoch time zero, which is midnight 1-Jan-1970, far before what WeeWX is expecting. WeeWX detects this very early time and sleeps until NTP can set the true time. Then it proceeds.

Depending on what version of Raspbian your RPi is running, there will be one of two versions of fake clocks. Try this,

~~~~~
$ sudo apt-get purge fake-hwclock
~~~~~

If that doesn't work, then try this:

~~~~~
$ sudo systemctl stop systemd-timesyncd
$ sudo systemctl disable systemd-timesyncd
~~~~~

#### C. Make weewxd depend on a valid system time

The article [How can I delay the startup of systemd services until the datetime is set (no RTC on the Raspberry Pi)
](https://raspberrypi.stackexchange.com/questions/94635/how-can-i-delay-the-startup-of-systemd-services-until-the-datetime-is-set-no-rt) has some very useful hints on a more elegant solution


### Use a high-quality SD card

The SD card supplied with most RPis seems to be of very low quality. Make sure you use a good, Class 10 card. User William Phelps reports, "You can usually spot a failing card by watching the kernel I/O wait time. Once the card starts to go, the I/O wait time will increase significantly."

Many users have had good luck with the better Sandisk SD cards. In increasing order of quality (and expense):

* Sandisk Extreme
* Sandisk Extreme Plus (what I use)
* Sandisk Extreme Pro

Historically there have been SD card issues on older pi. While this occurred most often on the old model-B pi, it does occur occasionally on the newer zero/zerow/pi3/pi3+/pi4 cards that use micro SD cards that have weak power supplies.


### Minimize writes to the SD card

It is possible to tweak the operating system so that it does not write to the SD as much.

Detailed instructions are in the [guide to minimizing writes](https://github.com/weewx/weewx/wiki/Minimize-writes-on-SD-cards).

### FTP

Many Raspbian versions of Debian do not seem to include an FTP client. You may have to install this if you plan on using FTP.

~~~~~
$ sudo apt-get install ftp
~~~~~


### Use a good power supply

A lot of problems with the RPi seem to center around inadequate power supplies.  Many weather stations can demand a lot of current through their USB connection. Make sure your RPi can supply it, either by getting a good power supply, or, alternatively, by using a powered USB hub.  I use an [Innogear 1.5 A supply](http://www.amazon.com/gp/product/B00J3IB7A2/), which costs well under $10 USD from Amazon, but, unfortunately, it is not offered any more. This [Samsung charger](https://www.amazon.com/Samsung-Adapter-5-Feet-Charging-Cables/dp/B00CQS0S7E/ref=sr_1_1) is essentially similar.
        
### Run a lightweight web server

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