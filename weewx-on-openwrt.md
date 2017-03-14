# Run weeWX on OpenWRT

This is a guide to running weeWX on OpenWRT with Oregon Scientific WMR200.

## Pre-Requisites

* OpenWRT Chaos Calmer 15.05

* Python packages from source (or maybe easy_install ?)
  * configobj 
    `http://pypi.upc.edu/mirror/configobj/configobj-5.0.6.tar.gz`
  * six 
    `http://pkgs.fedoraproject.org/repo/pkgs/python-six/six-1.10.0.tar.gz/34eed507548117b2ab523ab14b2f8b55/six-1.10.0.tar.gz`
  * pyusb 
    `http://internode.dl.sourceforge.net/project/pyusb/PyUSB%201.0/1.0.0-alpha-2/pyusb-1.0.0a2.tar.gz`

* Python packages from the repo 
  * python-sqlite3
  * python-decimal

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

Then to fix error with backend driver missing you need to help the drivers/wmr200.py driver file find the installed usb driver:

```
import usb

import usb.backend.libusb1
backend = usb.backend.libusb1.get_backend(find_library=lambda x: "/usr/lib/libusb-1.0.so")
```

Now it should run!

## init script

I added this startup init script:

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
       
}
```

## Credits

Hayden Thring

## References

https://groups.google.com/forum/#!topic/weewx-user/M8Jpy4Fosp0