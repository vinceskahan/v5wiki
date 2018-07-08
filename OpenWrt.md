# Run weeWX on OpenWRT on a travel router MT300N with Fine Offset WH1080 - WORK IN PROGRESS, NOT YET USABLE

This is a guide from Jul 2018 about running weeWX v3.8.0 on OpenWRT/Lede V17.01.4 on top of a a travel router [GL.iNET GL-MT300N-V2](https://www.gl-inet.com/products/gl-mt300n-v2/) which is a small pocket computer with less than 5W power consumption and a pricetag of less than 25,- EUR with a personal weather station Froggit WS1080SE which is identical with [Fine Offset WH1080](http://www.foshk.com/Weather_Professional/WH1080.html).

The setup provides the following features:
* reads out the weather data regularly (every 5 min in my case), 
* stores the data in a sqlite database, 
* builds a local webpage with the current weather data together with historically collected weather data 
* transfers the local webpages to a remote webserver via ftp
* transfers the weather data regularly to a series of public weahther data services

## Pre-Requisites

* A computer with internet access
* A browser (I use latest Firefox but others should work well as well)
* A terminal program, I use [putty](https://www.putty.org/)
* It is assumed that you have a dhcp server in your local LAN where newly plug-in devices get a proper network address assigned automatically!
* The travel router GL-MT300N-V2 (short MT300N)
* A simple usb hub, I use a cheap 4-port USB 2.0 hub from ebay for EUR 2,44 including shipping!
* A thumb drive aka as a usb flash drive, I use a give away with 16GB
* A powered USB HUB or a USB poer supply to power the MT300N, I have a USB hub connected to my computer with an external power supply and use this as power supply for the MT300N
* A free network port on your internet router / local network and a RJ45 patch kabel to connect the MT300N
 to your network an provide it with internet access

## Getting Started with the MT300N - Preparation of the pocket computer

* Power up the MT300N with the usb cable
* Connect the MT300N WAN port with the RJ45 cable to the free network port on your router
* Take the network cable which connects your computer to your network out of the router and plug it into the LAN port of the MT300
* Connect with the browser of choice to 192.168.8.1 (LAN-adress of the MT300N)
* Select a password for access to the MT300N
* Optional but recommended: deactivate WiFi
* Check if latest firmware is installed on MT300N and do so if not - after firmware upgrade you have to repeat the above
* Allow access from the WAN port to the MT300 (see following screenshot)
![Allow WAN access on the MT300N](https://farm1.staticflickr.com/919/29406483378_59cf108b0e_b.jpg)
* Select "Advanced setting >>" on the very top tight of the administration page and log in with the user root and the same password you selected at the beginning for the MT300N
* Go to System -> Administration in the top menu and enable SSH access through the WAN port (see following screenshot, all boxes marked on the screenshot need to be marked, do not forget to click "Save & Apply" at the end)
![Enable SSH access from WAN port](https://farm1.staticflickr.com/919/29406645338_c7367b698b_b.jpg)
* Then go to Network -> Firewall and allow all traffic from WAN through the WAN port (see following screenshot, replicate entries as shown and press "Save & Apply")
![Disable Firewall](https://farm2.staticflickr.com/1821/42558181244_736444a920_b.jpg)
IMPORTANT: with this last step you have disabled the firewall on the MT300N. Do not use it in this state as a router! Ensure you have protected you local network suffciently!
* Now you could unplug the network cable of your computer from the LAN port of the MT300N and plug it again back to your regular router
* Figure out (ideally through checking on your internet router) what ip adress the MT300N got assigned in your local network and try to access it through your web browser
* If the above worked, then open a SSH session to the MT300N
* Plug in your USB hub into the USB port of the MT300N and plug in the usb drive into the USB hub
* Extend the storage capacity on the MT300 to allow python installation and to run weewx by following the [Extroot description on OpenWrt.com](https://openwrt.org/docs/guide-user/additional-software/extroot_configuration]
* Now you should have sufficient capacity for the installation of python, weewx and for operating weewx

## Installation of Python, required modules and weewx





  * python-light
  * libusb-1.0
  * openssh-sftp-server (optional, provides sftp access, handy for file editing)

* Python packages from the repo
  * python-distutils
  * python-codecs
  * python-openssl
  * python-logging
  * python-email
  * python-decimal
  * python-ctypes
  * python-sqlite3

* Python packages from source (ie not in repo) "python setup.py install" (they are not from dev site, to not use https for wget)
  * configobj 
    `http://pypi.upc.edu/mirror/configobj/configobj-5.0.6.tar.gz`
  * six 
    `http://pkgs.fedoraproject.org/repo/pkgs/python-six/six-1.10.0.tar.gz/34eed507548117b2ab523ab14b2f8b55/six-1.10.0.tar.gz`
  * pyusb 
    `http://internode.dl.sourceforge.net/project/pyusb/PyUSB%201.0/1.0.0/pyusb-1.0.0.tar.gz`

* Weewx source code, Install section here: http://www.weewx.com/docs/setup.htm
(once installed you can save about 1mb by deleting the examples, docs and skins folders)

## weeWX Configuration

In weewx.conf I disabled reports to save installing some more packages: 

```
report_services = weewx.engine.StdPrint
```

Add the driver section:

```
[WMR200]
    # This section is for the Oregon Scientific WMR200
    
    # The station model, e.g., WMR200, WMR200A, Radio Shack W200
    model = WMR200
    
    # The driver to use:
    driver = weewx.drivers.wmr200
    
    archive_interval = 300
```

Then force archive interval for your station setting: in [WMR200] and [StdArchive] sections:

```
[StdArchive]
    
    # If the station hardware supports data logging then the archive interval
    # will be downloaded from the station. Otherwise, specify it (in seconds).
    archive_interval = 300
```

Then to fix error with backend driver missing you need to help the /home/weewx/bin/weewx/drivers/wmr200.py driver file find the installed usb driver:

```
import usb

import usb.backend.libusb1
backend = usb.backend.libusb1.get_backend(find_library=lambda x: "/usr/lib/libusb-1.0.so")
```

The above described issue and the fix does not only apply to the wmr200.py file but it seems relevant for all usb based drivers. It certainly applies as well for the fousb.py driver which supports a huge variety of weather stations manufactured by Fine Offset!

Now it should run! You can monitor system log for weewx messages in ssh -> "logread -f"

## init script

I added this startup init script: /etc/init.d/weewxd 

```
#!/bin/sh /etc/rc.common
# Simple weewkd initd for openwrt
# Note paths used if different.
# www.httech.com.au

START=99
STOP=99

start() {        
        logger "starting weewx"
        # commands to launch application
        /home/weewx/bin/weewxd --daemon /home/weewx/weewx.conf
}       

stop() {          
        logger "stopping weewx"
        # commands to kill application 
        kill -9 `cat /var/run/weewx.pid`        
}

restart() {          
        logger "restarting weewx"
        # commands to kill application 
        kill -HUP `cat /var/run/weewx.pid`
        /home/weewx/bin/weewxd --daemon /home/weewx/weewx.conf
}
```

## Credits

Hayden Thring

## References

https://groups.google.com/forum/#!topic/weewx-user/M8Jpy4Fosp0