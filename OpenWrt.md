# Run weeWX on OpenWRT on a travel router MT300N with Fine Offset WH1080

This is a guide from Jul 2018 about running weeWX v3.8.0 on OpenWrt/Lede V17.01.4 on top of a a travel router [GL.iNET GL-MT300N-V2](https://www.gl-inet.com/products/gl-mt300n-v2/) which is a small pocket computer with less than 5W power consumption and a [pricetag of less than 25,- EUR](https://www.amazon.de/GL-iNet-GL-MT300N-V2-Repeater-Performance-Compatible/dp/B073TSK26W/). In this setup the travel router and weewx are used to pull weather observation data from  a personal weather station Froggit WS1080SE which is identical with [Fine Offset WH1080](http://www.foshk.com/Weather_Professional/WH1080.html).

The setup provides the following features (mainly weewx feature set):
* reads out the weather data regularly (every 5 min in my case), 
* stores the data in a sqlite database, 
* builds a local webpage with the current weather data together with historically collected weather data 
* transfers the local webpages to a remote webserver via ftp
* transfers the weather data regularly to a series of public weahther data services

## Pre-Requisites

* A computer with internet access
* A browser (I use latest Firefox but others should work well as well)
* A terminal program, I use [putty](https://www.putty.org/)
* Some willingness to use shell commands and a basic understanding of how to use the editor vi. If no experience with vi is available, [here is a good start](https://www.howtogeek.com/102468/a-beginners-guide-to-editing-text-files-with-vi/)
* It is assumed that you have a DHCP server in your local LAN where newly pluged-in devices get a proper network address assigned automatically
* The travel router GL-MT300N-V2 (short MT300N)
* A simple usb hub, I use a cheap 4-port USB 2.0 hub from ebay for EUR 2,44 including shipping!
* A thumb drive aka usb flash drive or a HDD with at least 2GB of capacity which you can use permanently for this setup, I use a give-away from work with 16GB
* An externally powered USB HUB or a USB power supply to provide power for the MT300N, I have a USB hub connected to my computer with an external power supply and use this as power supply for the MT300N
* A free network port on your internet router / local network and a RJ45 patch cable to connect the MT300N
 to your network and provide it with internet access

## Getting Started with the MT300N - Preparation of the pocket computer

* Power up the MT300N with the usb cable
* Connect the MT300N WAN port with the RJ45 cable to the free network port on your router
* Take the network cable which connects your computer to your network out of the router and plug it into the LAN port of the MT300
* Connect with the browser of choice to 192.168.8.1 (LAN-adress of the MT300N)
* Select a password for access to the MT300N
* Optional but recommended: deactivate WiFi
* Check if latest firmware is installed on MT300N and do so if not - after firmware upgrade you have to repeat the above three steps (connect with browser, select password, deactivate WiFi)
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
* Extend the storage capacity on the MT300 to allow python installation and to run weewx by following the [Extroot description on OpenWrt.com](https://openwrt.org/docs/guide-user/additional-software/extroot_configuration)
* Now you should have sufficient storage capacity for the installation of python, weewx and for operating weewx

## Installation of Python and required modules

Before we start, log in to the MT300N via ssh and do

`opkg update`

Should run without an error.

Then we start installing the required packages

  * python-light, do `opkg install python-light`
  * libusb-1.0, should be already installed, otherwise do `opkg install libusb-1.0`
  * python-distutils, do `opkg install python-distutils`
  * python-codecs, do `opkg install python-codecs`
  * python-openssl, do `opkg install python-openssl`
  * python-logging, do `opkg install python-logging`
  * python-email, do `opkg install python-email`
  * python-decimal, do `opkg install python-decimal`
  * python-ctypes, do `opkg install python-ctypes`
  * python-sqlite3, do `opkg install python-sqlite3`
  * python-pil, !important! do `opkg install pillow`

Python packages from source which could not be installed as the packages above. You need to download the packages as source and follow the steps as follows
  * For some preparation, run the following commands (the first two commands create the directories for the weewx installation):
    * `mkdir /home`
    * `mkdir /home/weewx`
    * `mkdir /home/weewx/downloads` 
      (this creates a directory where we can store sources and downloads required during installation but we can empty the directory after everything runs)
    * `cd /home/weewx/downloads`

  * configobj, available from [https://pypi.org/project/ConfigObject/](https://files.pythonhosted.org/packages/9e/7d/2aca7320b9d2331dee9a4249d795374ef432379fac3bc29db145da079fd8/ConfigObject-1.2.2.tar.gz) but then you have to deal with https, ssl and wget
    * Download: to avoid wget-ssl issues I have downloaded the package to my private webserver from where you can get the package with 

      `wget http://lieberlinge.ddnss.de/downloads/configobj-5.0.6.tar.gz`
    * Extract: `tar -xvzf configobj-5.0.6.tar.gz`
    * Install: 

      `cd configobj-5.0.6/`

      `python setup.py install`

      `cd ..`

For the following modules repeat the above described procedure of download, extract and install with the respective paths
  * six, original available from https://pypi.org/project/six/, to avoid wget-ssl issues, you can do

    `wget http://lieberlinge.ddnss.de/downloads/six-1.10.0.tar.gz`
  * pyusb, original available from https://pypi.org/project/pyusb/, to avoid wget-ssl issues you can do

    `wget http://lieberlinge.ddnss.de/downloads/pyusb-1.0.1.tar.gz`
  * python-cheetah, original available from https://pypi.org/project/Cheetah/, to avoid wget-ssl, you can do 

    `wget http://lieberlinge.ddnss.de/downloads/cheetah-2.4.4.tar.gz`

## Installation of weeWX

* Download weeWX source code from http://weewx.com/downloads/ with:

  `wget http://weewx.com/downloads/weewx-3.8.0.tar.gz`
* Extract: `tar -xvzf weewx-3.8.0.tar.gz`
* Install:

   `cd weewx-3.8.0/`

   `./setup.py build`

   `./setup.py install`

Answer the asked questions about your station, but you can change the settings later as well

   `cd ../..`

## weeWX Configuration

Open the weewx configuration file for edit with
`vi weewx.conf`

Check the driver section:

`
[FineOffsetUSB]
    # This section is for the Fine Offset series of weather stations.

    # The station model, e.g., WH1080, WS1090, WS2080, WH3081
    model = WS2080

    # How often to poll the station for data, in seconds
    polling_interval = 60

    # The driver to use:
    driver = weewx.drivers.fousb`

There is an issue with weeWX on OpenWrt/LEDE as OpenWRT has no support for ldconfig and i didn't find an easy way to add ldconfig to OpenWRT/LEDE. But there is an easy hack to solve the issue, open the driver file with:

`vi bin/weewx/drivers/fousb.py`

scroll down until you find the line which reads

`import usb`
at approx line 250, then add the following two lines next

`import usb.backend.libusb1`

`backend = usb.backend.libusb1.get_backend(find_library=lambda x: "/usr/lib/libusb-1.0.so")`

and save the file fousb.py and quit the editor.

The above described issue and the fix does not only apply to the fousb.py but as well to wmr200.py file and it seems relevant for all usb based drivers!

Now it should run! You can monitor system log for weewx messages in ssh -> "logread -f"

## init script

I added this startup init script: /etc/init.d/weewxd which is copied without change from the wiki about [weewx on openwrt](weewx-on-openwrt) initially compiled by Hayden Thring

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

## Before you start with your weather station

I recommend to install the usbutils

`opkg install usbutils`

Which allows you to monitor what the operating system detects when you plug in the usb cable from your weather station into the USB hub connected to the MT300N. Before we plug in the weather station:

`lsusb -t

/:  Bus 02.Port 1: Dev 1, Class=root_hub, Driver=ohci-platform/1p, 12M

    |__ Port 1: Dev 2, If 0, Class=Hub, Driver=hub/4p, 12M

        |__ Port 2: Dev 3, If 0, Class=Mass Storage, Driver=usb-storage, 12M

/:  Bus 01.Port 1: Dev 1, Class=root_hub, Driver=ehci-platform/1p, 480M`

and after

`lsusb -t

/:  Bus 02.Port 1: Dev 1, Class=root_hub, Driver=ohci-platform/1p, 12M

    |__ Port 1: Dev 2, If 0, Class=Hub, Driver=hub/4p, 12M

        |__ Port 2: Dev 3, If 0, Class=Mass Storage, Driver=usb-storage, 12M

        |__ Port 3: Dev 4, If 0, Class=Human Interface Device, Driver=, 1.5M

/:  Bus 01.Port 1: Dev 1, Class=root_hub, Driver=ehci-platform/1p, 480M`

The weather station is detected by the system as Human Interface Device which is correct! Enjoy and have fun!

## Credits

Go to Hayden Thring for his excellent brilliant description here in the wiki about [weewx on openwrt](weewx-on-openwrt)!

## About the author

If you want to contact me directly, please send an email to [Bernhard Friess](mailto:bernhard.friess@gmx.de)

